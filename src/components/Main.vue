<template>
  <main class="main" id="main">
    <section class="about" id="scrollTarget">
      <div class="about__left">
        <h2 class="about__title">
          <img src="@/assets/img/about/title.svg" alt="about" height="47.058" width="320.43">
        </h2>
      </div>
      <div class="about__right" id="about">
        <article class="about__item">
          <p class="about__name" v-scroll="onScrollShow"><span class="about__nameInner">ふかいし</span></p>
          <p class="about__text" v-scroll="onScrollShow"><span class="about__textInner">デザイナーとかフロントエンドエンジニアみたいなことをやっています。</span></p>
          <p class="about__text" v-scroll="onScrollShow"><span class="about__textInner">今後「不快指数」というサークルで何かやるかもしれないし、やらないかもしれないです。</span></p>
          <p class="about__text" v-scroll="onScrollShow"><span class="about__textInner">アニメ・漫画・酒・服・スニーカー・エンタメ系のかっこいいデザイン・青山吉能さん・赤城あんなさん・田中摩美々さんとかが好きです。</span></p>
          <p class="about__text" v-scroll="onScrollShow"><span class="about__textInner">何かあればTwitterのDMに連絡ください。</span></p>
          <ul class="about__lists" v-scroll="onScrollShow">
            <li class="about__list" v-scroll="onScrollShow"><a class="about__listAnchor" href="https://twitter.com/fxkxxshx" target="_blank"><span class="about__listInner">Twitter</span></a></li>
            <li class="about__list" v-scroll="onScrollShow"><a class="about__listAnchor" href="https://github.com/fxkxxshx" target="_blank"><span class="about__listInner">GitHub</span></a></li>
          </ul>
        </article>
        <article class="about__item">
          <p class="about__name" v-scroll="onScrollShow"><span class="about__nameInner">不快指数</span></p>
          <p class="about__text" v-scroll="onScrollShow"><span class="about__textInner">ふかいし主宰のサークルです。とりあえず名前だけ決まってます。何かできると良いですね。</span></p>
        </article>
      </div>
    </section>
    <section class="work">
      <div class="work__left">
        <h2 class="work__title">
          <img src="@/assets/img/work/title.svg" alt="work" height="52.049" width="320.324">
        </h2>
      </div>
      <div class="work__right" id="work">
        <article class="work__item">
          <a class="work__itemAnchor" href="https://tekubi-hikaru.netlify.app/" target="_blank">
            <div class="work__itemLeft" v-scroll="onScrollShow">
              <p class="work__thumb"><img src="@/assets/img/work/thumb_03.jpg" alt="" height="1080" width="1920"></p>
            </div>
            <div class="work__itemRight" v-scroll="onScrollShow">
              <p class="work__name"><span class="work__nameInner">TEKUBI HIKARU</span></p>
              <p class="work__text"><span class="work__textInner">2022.07 / デザイン・コーディング</span></p>
            </div>
          </a>
        </article>
        <article class="work__item">
          <a class="work__itemAnchor" href="https://hub.vroid.com/characters/6370052186432543582/models/3982772858169024897" target="_blank">
            <div class="work__itemLeft" v-scroll="onScrollShow">
              <p class="work__thumb"><img src="@/assets/img/work/thumb_02.jpg" alt="" height="1080" width="1920"></p>
            </div>
            <div class="work__itemRight" v-scroll="onScrollShow">
              <p class="work__name"><span class="work__nameInner">ふかいしのキャラクター</span></p>
              <p class="work__text"><span class="work__textInner">2021.11 / VRoid Studio</span></p>
            </div>
          </a>
        </article>
        <article class="work__item">
          <a class="work__itemAnchor" href="https://fkxsh.com" target="_blank">
            <div class="work__itemLeft" v-scroll="onScrollShow">
              <p class="work__thumb"><img src="@/assets/img/work/thumb_01.jpg" alt="" height="1080" width="1920"></p>
            </div>
            <div class="work__itemRight" v-scroll="onScrollShow">
              <p class="work__name"><span class="work__nameInner">ふかいしのWebサイト</span></p>
              <p class="work__text"><span class="work__textInner">2021.05 / デザイン・コーディング</span></p>
            </div>
          </a>
        </article>
      </div>
    </section>
    <p class="qr"><img src="@/assets/img/qr/qr.svg" alt="" height="50px" width="50px"></p>
  </main>
</template>

<script>
export default {
  name: 'Main',
  directives: {
    scroll: {
      inserted (el, binding) {
        const target = document.getElementById('app');
        const f = function(evt) {
          if (binding.value(evt, el)) {
            target.removeEventListener('scroll', f);
          }
        }
        target.addEventListener('scroll', f);
      }
    }
  },
  methods: {
    onScrollShow (evt, el) {
      const top = el.getBoundingClientRect().top;
      if (window.scrollY > top - window.innerHeight * 0.75) {
        el.classList.add('is-show');
        return true;
      }
      return false;
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="scss">
@import "@/assets/scss/var.scss";
@import "@/assets/scss/mixin.scss";

.main {
  padding: calc(100vh + 120px) 25px 25px;
  @include tb {
    padding: calc(100vh + 160px) 0 45px;
    width: 80%;
    margin: 0 auto;
  }

  .about {
    margin: 0 0 120px;
    @include tb {
      display: flex;
      margin: 0 0 160px;
    }

    &__left {
      position: sticky;
      top: 45vh;
      @include tb {
        padding: 0 80px 0 0;
        position: relative;
        top: 0;
        width: 30%;
      }
    }

    &__title {
      margin: 0;
      font-size: 0;
      width: 100%;
      @include tb {
        position: sticky;
        top: 45vh;
      }

      img {
        height: auto;
        width: 100%;
      }
    }

    &__right {
      margin: 60px 0 0;
      padding: 0 0 0 28px;
      position: relative;
      @include tb {
        margin: 0;
        padding: 0 0 0 33px;
        width: 70%;
      }

      &::before {
        background: $mainColor;
        content: '';
        height: 100%;
        left: 5px;
        position: absolute;
        top: 0;
        width: 3px;
        @include tb {
          left: 6px;
        }
      }
    }

    &__item {
      position: relative;

      &:nth-of-type(1) {

        &::after {
          content: '#p';
        }
      }

      &:nth-of-type(2) {

        &::after {
          content: '#c';
        }
      }

      &::before {
        background: $subColor;
        border: 3px solid $mainColor;
        border-radius: 50%;
        box-sizing: border-box;
        content: '';
        height: 13px;
        left: -28px;
        position: absolute;
        top: 0;
        width: 13px;
        @include tb {
          height: 15px;
          left: -33px;
          width: 15px;
        }
      }

      &::after {
        color: $mainColor;
        content: '';
        font-size: 10px;
        left: -42px;
        position: absolute;
        text-align: right;
        top: 2px;
        @include rg-r;
        @include tb {
          font-size: 12px;
          left: -50px;
        }
      }

      & + .about__item {
        margin: 60px 0 0;
        @include tb {
          margin: 80px 0 0;
        }
      }
    }

    &__name {
      margin: 0 0 15px;
      padding: 0 0 16px;
      position: relative;
      transform: scale(0, 1);
      transform-origin: left top;
      transition: transform 0.2s ease-out;
      @include tb {
        margin: 0 0 18px;
        padding: 0 0 19px;
      }

      &.is-show {
        transform: scale(1,1);

        &::before {
          transform: scale(1,1);
        }

        .about__nameInner {
          color: $subColor;
        }
      }

      &::before {
        background: $mainColor;
        bottom: 0;
        content: '';
        height: 1px;
        left: 0;
        position: absolute;
        transform: scale(0,1);
        transform-origin: left top;
        transition: transform 0.2s ease-out 0.3s;
        width: 12px;
        @include tb {
          width: 14px;
        }
      }

      &Inner {
        background: $mainColor;
        color: transparent;
        font-size: 24px;
        box-decoration-break: clone;
        display: inline;
        line-height: 1.55;
        padding: 1px 6px;
        transition: color 0.3s ease-out 0.4s;
        @include ns_b;
        @include tb {
          font-size: 28px;
          line-height: 1.5;
        }
      }
    }

    &__text {
      margin: 0 0 20px;
      padding: 0;
      transform: scale(0,1);
      transform-origin: left top;
      transition: transform 0.2s ease-out;
      @include tb {
        margin: 0 0 23px;
      }

      &.is-show {
        transform: scale(1,1);

        &:last-of-type {

          &::before {
            transform: scale(1,1);
          }
        }

        .about__textInner {
          color: $subColor;
        }
      }

      &:last-of-type {
        margin: 0;
        padding: 0 0 16px;
        position: relative;
        @include tb {
          padding: 0 0 19px;
        }

        &::before {
          background: $mainColor;
          bottom: 0;
          content: '';
          height: 1px;
          left: 0;
          position: absolute;
          transform: scale(0,1);
          transform-origin: left top;
          transition: transform 0.2s ease-out 0.3s;
          width: 12px;
          @include tb {
            width: 14px;
          }
        }
      }

      &Inner {
        background: $mainColor;
        color: transparent;
        font-size: 14px;
        box-decoration-break: clone;
        display: inline;
        line-height: 2;
        padding: 4px 6px;
        transition: color 0.4s ease-out 0.3s;
        @include ns_m;
        @include tb {
          font-size: 16px;
        }
      }
    }

    &__lists {
      display: flex;
      margin: 15px 0 0;
      padding: 0 0 16px;
      position: relative;
      @include tb {
        margin: 18px 0 0;
        padding: 0 0 19px;
      }

      &.is-show {

        &::before {
          transform: scale(1,1);
        }
      }

      &::before {
        background: $mainColor;
        bottom: 0;
        content: '';
        height: 1px;
        left: 0;
        position: absolute;
        transform: scale(0,1);
        transform-origin: left top;
        transition: transform 0.2s ease-out 0.3s;
        width: 12px;
        @include tb {
          width: 14px;
        }
      }
    }

    &__list {
      list-style: none;
      transform: scale(0,1);
      transform-origin: left top;
      transition: transform 0.2s ease-out;

      &.is-show {
        transform: scale(1, 1);

        .about__listAnchor {
          color: $subColor;

          &::before {
            opacity: 1;
          }
        }
      }

      & + .about__list {
        margin: 0 0 0 15px;
        @include tb {
          margin: 0 0 0 18px;
        }
      }

      &Anchor {
        color: transparent;
        position: relative;
        text-decoration: none;
        transition: color 0.4s ease-out 0.3s;

        &:hover {
          @include tb {

            &::before {
              background: transparent;
            }

            .about__listInner {
              background: #000;
            }
          }
        }

        &::before {
          background: $subColor;
          bottom: -1px;
          content: '';
          height: 1px;
          left: 0;
          margin: 0 auto;
          opacity: 0;
          position: absolute;
          right: 0;
          transition: opacity 0.4s ease-out 0.3s, background 0.2s ease-out;
          width: calc(100% - 12px);
        }
      }

      &Inner {
        background: $mainColor;
        font-size: 14px;
        box-decoration-break: clone;
        display: inline;
        line-height: 2;
        padding: 4px 6px;
        transition: background 0.3s ease-out;
        @include ns_m;
        @include tb {
          font-size: 16px;
        }   
      }
    }
  }

  .work {
    margin: 0 0 120px;
    @include tb {
      display: flex;
      margin: 0 0 160px;
    }

    &__left {
      position: sticky;
      top: 45vh;
      @include tb {
        padding: 0 80px 0 0;
        position: relative;
        top: 0;
        width: 30%;
      }
    }

    &__title {
      margin: 0;
      font-size: 0;
      width: 100%;
      @include tb {
        position: sticky;
        top: 45vh;
      }

      img {
        height: auto;
        width: 100%;
      }
    }

    &__right {
      margin: 60px 0 0;
      padding: 0 0 0 28px;
      position: relative;
      @include tb {
        margin: 0;
        padding: 0 0 0 33px;
        width: 70%;
      }

      &::before {
        background: $mainColor;
        content: '';
        height: 100%;
        left: 5px;
        position: absolute;
        top: 0;
        width: 3px;
        @include tb {
          left: 6px;
        }
      }
    }

    &__item {
      position: relative;

      &:nth-of-type(1) {

        &::after {
          content: '02';
        }
      }

      &:nth-of-type(2) {

        &::after {
          content: '01';
        }
      }

      &::before {
        background: $subColor;
        border: 3px solid $mainColor;
        border-radius: 50%;
        box-sizing: border-box;
        content: '';
        height: 13px;
        left: -28px;
        position: absolute;
        top: 0;
        width: 13px;
        @include tb {
          height: 15px;
          left: -33px;
          width: 15px;
        }
      }

      &::after {
        color: $mainColor;
        content: '#1';
        font-size: 10px;
        left: -42px;
        line-height: 1;
        position: absolute;
        text-align: right;
        top: 2px;
        @include rg-r;
        @include tb {
          font-size: 12px;
          left: -50px;
        }
      }

      & + .work__item {
        margin: 60px 0 0;
        @include tb {
          margin: 80px 0 0;
        }
      }

      &Anchor {
        color: $subColor;
        text-decoration: none;
        @include pc {
          display: flex;
          padding: 0 0 5px;
        }

        &:hover {
          @include tb {
            .work__itemLeft{

              .work__thumb {
                border: 1px solid #000;
                // overflow: hidden;
                transform: translate(5px, 5px);

                &::before {
                  // background: transparent;
                  transform: translate(-5px, -5px);
                }
              }
            }

            .work__nameInner {
              background: #000;
            }

            .work__text {

              &:last-of-type {

                &::before {
                  background: #000;
                }
              }
            }

            .work__textInner {
              background: #000;
            }
          }
        }
      }

      &Left {
        @include pc {
          width: 50%;
        }

        &.is-show {

          .work__thumb {
            
            &::before {
              transform: translate(0, 0);
            }
          }
        }
      }

      &Right {
        margin: 19px 0 0;
        transform-origin: left top;
        @include tb {
          margin: 23px 0 0;
        }
        @include pc {
          box-sizing: border-box;
          display: flex;
          flex-direction: column;
          margin: 0 0 -5px;
          padding: 0 0 0 23px;
          width: 50%;
        }

        &.is-show {

          .work__name {
            transform: scale(1, 1);
          }

          .work__nameInner {
            color: $subColor;
          }

          .work__text {
            transform: scale(1, 1);

            &:last-of-type {

              &::before {
                transform: scale(1,1);
              }
            }
          }

          .work__textInner {
            color: $subColor;
          }
        }
      }
    }

    &__thumb {
      border: 1px solid $mainColor;
      border-radius: 30px;
      box-sizing: border-box;
      font-size: 0;
      margin: 0;
      position: relative;
      transition: border 0.3s ease-out, transform 0.3s ease-out;
      width: calc(100% - 4px);
      z-index: 1;
      @include tb {
        width: calc(100% - 5px);
      }

      &::before {
        background: $mainColor;
        border-radius: 30px;
        content: '';
        height: 100%;
        left: 4px;
        position: absolute;
        top: 4px;
        transform: translate(-4px, -4px);
        transition: transform 0.3s ease-out;
        width: 100%;
        @include tb {
          left: 5px;
          top: 5px;
          transform: translate(-5px, -5px);
        }
      }

      img {
        border-radius: 30px;
        height: auto;
        position: relative;
        width: 100%;
        z-index: 1;
      }
    }

    &__name {
      margin: 0;
      transform: scale(0, 1);
      transform-origin: left top;
      transition: transform 0.2s ease-out;
      @include pc {
        margin: auto 0 0;
      }

      &Inner {
        background: $mainColor;
        color: transparent;
        font-size: 24px;
        box-decoration-break: clone;
        display: inline;
        line-height: 1.55;
        padding: 1px 6px;
        transition: background 0.3s ease-out, color 0.3s ease-out 0.4s;
        @include ns_b;
        @include tb {
          font-size: 28px;
          line-height: 1.5;
        }
      }
    }

    &__text {
      margin: 0 0 20px;
      padding: 0;
      transform: scale(0,1);
      transform-origin: left top;
      transition: transform 0.2s ease-out;
      @include tb {
        margin: 0 0 23px;
      }

      &:last-of-type {
        margin: 0;
        padding: 0 0 16px;
        position: relative;
        @include tb {
          padding: 0 0 19px;
        }

        &::before {
          background: $mainColor;
          bottom: 0;
          content: '';
          height: 1px;
          left: 0;
          position: absolute;
          transform: scale(0,1);
          transform-origin: left top;
          transition: background 0.3s ease-out, transform 0.2s ease-out 0.3s;
          width: 12px;
          @include tb {
            width: 14px;
          }
        }
      }

      &Inner {
        background: $mainColor;
        color: transparent;
        font-size: 14px;
        box-decoration-break: clone;
        display: inline;
        line-height: 2;
        padding: 4px 6px;
        transition: background 0.3s ease-out, color 0.3s ease-out 0.4s;
        @include ns_m;
        @include tb {
          font-size: 16px;
        }
      }
    }
  }

  .qr {
    font-size: 0;
    margin: 0;
    position: relative;
    width: 45px;
    @include tb {
      width: 50px;
    }

    img {
      height: auto;
      width: 100%;
    }
  }
}
</style>
