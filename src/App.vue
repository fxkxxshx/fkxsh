<template>
  <div id="app">
    <Chara/>
    <Header/>
    <Main/>
    <Footer/>
    <Loading/>
    <ThemeEditor/>
  </div>
</template>

<script>
import Chara from './components/Chara.vue';
import Header from './components/Header.vue';
import Main from './components/Main.vue';
import Footer from './components/Footer.vue';
import Loading from './components/Loading.vue';
import ThemeEditor from './components/ThemeEditor.vue';

export default {
  name: 'App',
  components: {
    Chara,
    Header,
    Main,
    Footer,
    Loading,
    ThemeEditor
  },
  mounted () {
    window.addEventListener('DOMContentLoaded', this.onHeight);
    window.addEventListener('resize', this.onHeight);
  },
  methods: {
    onHeight () {
      const target = document.getElementById('app');
      target.style.height = window.innerHeight + 'px';
    }
  }
}
</script>

<style lang="scss">
@import "@/assets/scss/var.scss";
@import "@/assets/scss/mixin.scss";

:root {
  --color-primary: #0000ff;
  --color-surface: #f5f5f5;
  --color-ink: #000000;
}

#app {
  height: 100vh;
  overflow-y: scroll;
  overscroll-behavior: none;
  -webkit-overflow-scrolling: touch;
}
</style>
