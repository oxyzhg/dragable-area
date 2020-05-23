<template>
  <div
    ref="dragableArea"
    class="dragable-wrap"
    :style="style"
    @touchmove.prevent
    @mousemove.prevent
    @mousedown="mouseDown"
    @mouseup="mouseUp"
  >
    <slot>
      <div class="block">A</div>
    </slot>
  </div>
</template>

<script>
export default {
  name: 'DragableArea',

  props: {
    distanceRight: {
      type: Number,
      default: 0,
    },
    distanceBottom: {
      type: Number,
      default: 100,
    },
    gutterX: {
      type: Number,
      default: 50,
    },
    gutterY: {
      type: Number,
      default: 200,
    },
    zIndex: {
      type: [Number, String],
      default: 50,
    },
  },

  data() {
    return {
      floatWrapRef: null,
      floatWrapDOM: null,

      // client
      clientWidth: null,
      clientHeight: null,

      // position
      top: 0,
      left: 0,

      // property
      clickable: false,
      inMotion: false,
    };
  },

  computed: {
    style() {
      const { top, left, zIndex } = this;
      return {
        top: `${top}px`,
        left: `${left}px`,
        zIndex: zIndex,
      };
    },
  },

  methods: {
    // 初始化
    initWrap() {
      this.floatWrapRef.addEventListener('touchstart', this.touchStart);
      this.floatWrapRef.addEventListener('touchmove', this.touchMove);
      this.floatWrapRef.addEventListener('touchend', this.touchEnd);
    },

    // 监听 resize
    handleResize() {
      this.clientWidth = document.documentElement.clientWidth;
      this.clientHeight = document.documentElement.clientHeight;
      this.checkWrapPosition();
    },

    // PC端拖动
    mouseDown(e) {
      const event = e || window.event;

      const floatWrapWidth = this.floatWrapDOM.width / 2;
      const floatWrapHeight = this.floatWrapDOM.height / 2;

      event.preventDefault && event.preventDefault();

      this.floatWrapRef.style.transition = 'none';

      document.onmousemove = (e) => {
        const event = e || window.event;
        this.left = event.clientX - floatWrapWidth;
        this.top = event.clientY - floatWrapHeight;
      };
    },
    mouseUp() {
      document.onmousemove = null;
      // check position
      this.checkWrapPosition();
      this.floatWrapRef.style.transition = 'all 0.3s';
    },

    // 移动端拖动
    touchStart() {
      this.clickable = false;
      this.floatWrapRef.style.transition = 'none';
    },
    touchMove(e) {
      this.clickable = true;

      if (e.targetTouches.length === 1) {
        // 单指拖动
        const touch = e.targetTouches[0];
        this.left = touch.clientX - this.floatWrapDOM.width / 2;
        this.top = touch.clientY - this.floatWrapDOM.height / 2;
      }
    },
    touchEnd() {
      if (!this.clickable) return;
      this.floatWrapRef.style.transition = 'all 0.3s';
      this.checkWrapPosition();
    },

    // 检查定位
    checkWrapPosition() {
      const { left, top, floatWrapDOM, clientWidth, clientHeight, gutterX, gutterY } = this;
      // 中点是否过Y中轴线
      const isMoveAxisY = left + floatWrapDOM.width / 2 >= clientWidth / 2;

      this.left = isMoveAxisY ? clientWidth - floatWrapDOM.width - gutterX : gutterX;

      // 判断是否超出屏幕上沿
      if (top < gutterY) {
        this.top = gutterY;
      }
      // 判断是否超出屏幕下沿
      if (top + floatWrapDOM.height + gutterY >= clientHeight) {
        this.top = clientHeight - floatWrapDOM.height - gutterY;
      }
    },
  },
  created() {
    this.clientWidth = document.documentElement.clientWidth;
    this.clientHeight = document.documentElement.clientHeight;
  },
  mounted() {
    this.$nextTick(() => {
      this.floatWrapRef = this.$refs.dragableArea;
      // ElementRect
      this.floatWrapDOM = this.floatWrapRef.getBoundingClientRect();

      // default position
      this.left = this.clientWidth - this.floatWrapDOM.width - this.distanceRight;
      this.top = this.clientHeight - this.floatWrapDOM.height - this.distanceBottom;

      this.initWrap();
    });
    window.addEventListener('resize', this.handleResize);
  },
  beforeDestroy() {
    // window.removeEventListener('scroll', this.handleScroll);
    window.removeEventListener('resize', this.handleResize);
  },
};
</script>

<style scoped>
.dragable-wrap {
  position: fixed;
}

.block {
  width: 100px;
  height: 100px;
  background: #eee;
  border: 1px solid #000;
  line-height: 100px;
  text-align: center;
}
</style>
