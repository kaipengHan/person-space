<template>
  <div class="blog-toc-container">
    <h2>目录</h2>
    <RightMenu :list="tocWithSelect" @select="handleSelect"/>
  </div>
</template>

<script>
import RightMenu from "@/pages/Blog/components/RightMenu.vue";
import debounce from "@/utils/debounce";

export default {
  name: "BlogTOC",
  props: {
    toc: {
      type: Array,
    }
  },
  data() {
    return {
      activeAnchor: '',
    }
  },
  components: {
    RightMenu
  },
  computed: {
    // 根据toc属性以及activeAnchor得到带有isSelect属性的toc数组
    tocWithSelect() {
      const getToc = (toc = []) => {
        return toc.map(t => ({
          ...t,
          isSelect: t.anchor === this.activeAnchor,
          children: getToc(t.children),
        }))
      }
      return getToc(this.toc);
    },
    doms() {
      const doms = [];
      const addToDoms = (toc) => {
        for (const t of toc) {
          doms.push(document.getElementById(t.anchor));
          if (t.children && t.children.length) {
            addToDoms(t.children);
          }
        }
      }
      addToDoms(this.toc);
      return doms;
    }
  },
  created() {
    this.setSelectDebounce = debounce(this.setSelect, 50);
    this.$bus.$on("mainScroll", this.setSelectDebounce);
  },
  destroyed() {
    this.$bus.$off('mainScroll', this.setSelectDebounce);
  },
  methods: {
    handleSelect(item) {
      location.hash = item.anchor;
    },
    setSelect() {
      this.activeAnchor = '';
      const range = 200;
      for (const dom of this.doms) {
        if (!dom) {
          continue;
        }
        // 得到元素离视口顶部的距离
        const top = dom.getBoundingClientRect().top;
        if (top >= 0 && top <= range) {
          // 在规定的范围内
          this.activeAnchor = dom.id;
          return;
        } else if (top > range) {
          // 在规定的范围下方
          return;
        } else {
          // 在规定的范围上方
          this.activeAnchor = dom.id; // 先假设自己是激活的，然后继续看后面
        }
      }
    }
  }
}
</script>

<style scoped lang="less">
.blog-toc-container {
  h2 {
    font-weight: bold;
    letter-spacing: 2px;
    font-size: 1em;
    margin: 0;
  }
}
</style>
