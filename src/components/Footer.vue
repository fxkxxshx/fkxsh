<template>
  <footer class="footer">
    <p class="footer__copy">&copy; 2021 fkxsh</p>
  </footer>
</template>

<script>
export default {
  name: 'Footer'
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="scss">
@import "@/assets/scss/var.scss";
@import "@/assets/scss/mixin.scss";

.footer {

  &__copy {
    bottom: 25px;
    color: $oreRed;
    font-size: 10px;
    margin: 0;
    padding: 0;
    position: fixed;
    right: 25px;
    z-index: 100;
    @include rg-r;
    @include tb {
      bottom: 45px;
      font-size: 13px;
      right: calc(144 / 1440 * 100%);
    }
  }
}
</style>