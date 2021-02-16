<template>
  <div class="bbs">
    <a
      class="bbs"
      href="https://discuss.sharemoe.net/"
      target="_Blank"
    />
  </div>
</template>

<script>
export default {
  name: 'BBS',
};
</script>

<style scoped lang="less">
.bbs {
  height: calc(~"100vh - 60px");
  width: calc(~"100vw-65px");
  overflow: auto;
  background: #f5f5f5;
  display: flex;

  .iframe{
    height: 100%;
    width: 100%;

    // fix: firefox replaced elements overflow
    iframe {
      display: block;
    }
  }
}
</style>
