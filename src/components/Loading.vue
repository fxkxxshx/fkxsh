<template>
  <div class="loading" id="loading">
    <p class="loading__text">loading...</p>
  </div>
</template>

<script>
export default {
  name: 'Loading',
  props: {
  },
  data () {
    return {
    };
  },
  mounted () {
    window.addEventListener('DOMContentLoaded', this.onHeight);
    window.addEventListener('resize', this.onHeight);
    window.addEventListener('load', this.onLoaded);
  },
  methods: {
    onHeight () {
      const target = document.getElementById('loading');
      target.style.height = window.innerHeight + 'px';
    },
    onLoaded () {
      const target = document.getElementById('loading');
      target.classList.add('is-hide');
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="scss">
@import "@/assets/scss/var.scss";
@import "@/assets/scss/mixin.scss";

.loading {
  align-items: center;
  background: $mainColor;
  display: flex;
  height: 100vh;
  justify-content: center;
  left: 0;
  margin: 0;
  overflow: hidden;
  overscroll-behavior: none;
  position: fixed;
  top: 0;
  width: 100%;
  z-index: 200;
  @include rg-ew;

  &.is-hide {
    animation: loadingHide 0.6s forwards ease-in-out 2s;
    transform: translate(0, 0);
  }

  &__text {
    animation: loadingText 0.2s infinite linear;
    color: $subColor;
    font-size: 18px;
    opacity: 1;
    @include tb {
      font-size: 22px;
    }
  }
}

@keyframes loadingHide {
  0% {
    transform: translate(0, 0);
  }
  99% {
    transform: translate(0, -100%);
  }
  100% {
    display: none;
    transform: translate(0, -100%);
  }
}

@keyframes loadingText {
  0% {
    opacity: 1;
  }
  50% {
    opacity: 0;
  }
  100% {
    opacity: 1;
  }
}
</style>