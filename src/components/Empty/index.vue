<script>
import Icon from "@/components/Icon/index.vue";

export default {
  props: {
    text: {
      type: String,
      default: '无数据'
    }
  },
  components: {
    Icon
  }
}
</script>

<template>
  <div class="empty-container">
    <Icon type="empty"/>
    <p>{{ text }}</p>
  </div>
</template>

<style scoped lang="less">
@import "~@/styles/mixin.less";
@import "~@/styles/var.less";
.empty-container {
  .self-center();
  color: @gray;
  text-align: center;
}
.iconfont{
  font-size: 72px;
}
</style>
