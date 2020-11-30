<!-- 
参数:
msg: data数据
direction: row-上下，col-横向
speed：速度，默认为2
-->
<template>
  <div class="seamlessScrolling" ref="seamlessScrolling">
    <div
      class="marqueeContent"
      :class="{
        row: direction == 'row',
        col: direction == 'col',
        animate: animation,
      }"
      :style="contentStyle"
      ref="marqueeContent"
    >
      <div class="marqueeData" ref="data"><slot></slot></div>
      <div class="marqueeCopy" ref="copy" v-show="copyShow"><slot></slot></div>
    </div>
  </div>
</template>
<script>
export default {
  name: "seamlessScrolling",
  props: {
    msg: {
      type: Array,
      default() {
        return [];
      },
    },
    direction: {
      type: String,
      default: "row",
    },
    speed: {
      type: Number,
      default: 2,
    },
  },
  data() {
    return {
      animation: true,
      contentStyle: {},
      copyShow: true,
    };
  },
  watch: {
    msg() {
      this.$nextTick(() => {
        this.handleAnimation();
      });
    },
  },
  mounted() {
    this.handleAnimation();
  },
  methods: {
    handleAnimation() {
      const wrapWidth = this.$refs.seamlessScrolling.getBoundingClientRect()
        .width;
      const wrapHeight = this.$refs.seamlessScrolling.getBoundingClientRect()
        .height;
      const contentWidth = this.$refs.data.getBoundingClientRect().width;
      const contentHeifht = this.$refs.data.getBoundingClientRect().height;
      // 如果滚动内容 小于 外面盒子的宽度/高度 再插入dom，不进行无缝滚动
      if (
        (this.direction == "col" && contentHeifht < wrapHeight) ||
        (this.direction == "row" && contentWidth < wrapWidth)
      ) {
        this.animation = false;
        this.copyShow = false;
      } else {
        this.animation = true;
        this.copyShow = true;
      }
      const msgLength = this.$refs.data.children.length;
      this.contentStyle = {
        animationDuration: msgLength * 2 * this.speed + "s",
        animationDelay: "0.01s",
      };
    },
  },
};
</script>

<style lang="less" scoped>
.seamlessScrolling * {
  -webkit-backface-visibility: hidden;
}
.seamlessScrolling {
  width: 100%;
  height: 100%;
  overflow: hidden;
  position: relative;
  transform: translate3d(0, 0, 0);
  -webkit-transform: translate3d(0, 0, 0);
  .row {
    display: inline-block;
    white-space: nowrap;
    &.animate {
      animation: marqueeRow linear infinite;
    }
    .marqueeData,
    .marqueeCopy {
      display: inline-block;
      white-space: nowrap;
    }
  }
  .col {
    width: 100%;
    &.animate {
      -webkit-backface-visibility: hidden;
      backface-visibility: hidden;
      animation: marqueeCol linear infinite;
    }
  }
  &:hover .marquee-content {
    -webkit-animation-play-state: paused;
    animation-play-state: paused;
  }
  @keyframes marqueeRow {
    0% {
      transform: translate3d(0, 0, 0);
    }
    100% {
      transform: translate3d(-50%, 0, 0);
    }
  }
  @keyframes marqueeCol {
    0% {
      transform: translate3d(0, 0, 0);
    }
    100% {
      transform: translate3d(0, -50%, 0);
    }
  }
}
</style>
