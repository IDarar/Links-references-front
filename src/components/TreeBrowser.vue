<template>
  <div>
    <div 
    :style="{'margin-left': `${depth*20}px`}"
    class="str">
    <div class="linktree">{{ str.link }}</div>
  </div>
  <TreeBrowser 
    v-for="str  in str.refs"
    :key="str.link"
    :str="str"
    :depth="depth +1"
  />
  </div>
</template>
<script>
export default {
  name : 'TreeBrowser',
  props: {
    str: Object,
    depth:{
      type: Number,
      default:0,
    }
  },
};
</script>
<style scoped>
.linktree{
  font-size: 14px;
  padding-bottom: 20px;
}
</style>