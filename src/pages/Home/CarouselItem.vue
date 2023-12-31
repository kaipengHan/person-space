<!-- 当某个元素width：auto时过渡效果没有解决方案 -->
<script>
import ImageLoader from "@/components/ImageLoader/index.vue";

export default {
  name: "CarouselItem",
  props: ['carousel'],
  components: {
    ImageLoader,
  },
  data() {
    return {
      titleWidth: 0,
      descWidth: 0,
      mouseX: 0, // 鼠标的横坐标
      mouseY: 0, // 鼠标的纵坐标
      containerSize: null,
      innerSize: null,
    }
  },
  mounted() {
    this.titleWidth = this.$refs.title.offsetWidth;
    this.descWidth = this.$refs.desc.offsetWidth;
    this.setSize();
    this.mouseX = this.center.x;
    this.mouseY = this.center.y;
    window.addEventListener('resize', this.setSize);
  },
  destroyed() {
    window.removeEventListener('resize', this.setSize);
  },
  computed: {
    imagePosition() {
      if (!this.containerSize || !this.innerSize) {
        return;
      }
      const extraWidth = this.innerSize.width - this.containerSize.width;
      const extraHeight = this.innerSize.height - this.containerSize.height;
      const left = (-extraWidth / this.containerSize.width) * this.mouseX;
      const top = (-extraHeight / this.containerSize.height) * this.mouseY;
      return {
        transform: `translate(${left}px, ${top}px)`,
      };
    },
    center() {
      return {
        x: this.containerSize.width / 2,
        y: this.containerSize.height / 2
      }
    }
  },
  methods: {
    showWords() {
      this.$refs.title.style.opacity = 1;
      this.$refs.title.style.width = 0;
      this.$refs.title.clientHeight;
      this.$refs.title.style.transition = '1s';
      this.$refs.title.style.width = this.titleWidth + 'px';

      this.$refs.desc.style.opacity = 1;
      this.$refs.desc.style.width = 0;
      // 强制让浏览器渲染一次
      this.$refs.desc.clientWidth; // reflow
      this.$refs.desc.style.transition = "2s 1s";
      this.$refs.desc.style.width = this.descWidth + "px";
    },
    setSize() {
      this.containerSize = {
        width: this.$refs.container.clientWidth,
        height: this.$refs.container.clientHeight,
      };
      this.innerSize = {
        width: this.$refs.image.clientWidth,
        height: this.$refs.image.clientHeight,
      }
    },
    handleMouseMove(e) {
      const rect = this.$refs.container.getBoundingClientRect();
      this.mouseX = e.clientX - rect.left;
      this.mouseY = e.clientY - rect.top;
    },
    handleMouseLeave() {
      this.mouseX = this.center.x;
      this.mouseY = this.center.y;
    }
  }
}
</script>

<template>
  <div class="carousel-item-container" ref="container" @mousemove="handleMouseMove" @mouseleave="handleMouseLeave">
    <div class="carousel-img" ref="image" :style="imagePosition">
      <ImageLoader @load="showWords" :placeholder="carousel.midImg" :src="carousel.bigImg"/>
    </div>
    <div class="title" ref="title">{{ carousel.title }}</div>
    <div class="desc" ref="desc">{{ carousel.description }}</div>
  </div>
</template>

<style scoped lang="less">
@import "~@/styles/var.less";

.carousel-item-container {
  width: 100%;
  height: 100%;
  color: #fff;
  position: relative;
  overflow: hidden;
}

.carousel-img {
  width: 110%;
  height: 110%;
  position: absolute;
  left: 0;
  top: 0;
  transition: .3s;
}

.title,
.desc {
  position: absolute;
  letter-spacing: 3px;
  left: 30px;
  color: #fff;
  text-shadow: 1px 0 0 rgba(0, 0, 0, 0.5), -1px 0 0 rgba(0, 0, 0, 0.5),
  0 1px 0 rgba(0, 0, 0, 0.5), 0 -1px 0 rgba(0, 0, 0, 0.5);
  white-space: nowrap;
  overflow: hidden;
  opacity: 0;
}

.title {
  top: calc(50% - 40px);
  font-size: 2em;
}

.desc {
  top: calc(50% + 20px);
  font-size: 1.2em;
  color: lighten(@gray, 20%);
}
</style>
