<template>
  <main class="main" id="main">
    <section class="about" id="scrollTarget">
      <div class="about__left">
        <h2 class="about__title">
          <span class="about__titleText">about</span>
        </h2>
      </div>
      <div class="about__right" id="about">
        <article class="about__item">
          <p class="about__name" v-scroll="onScrollShow"><span class="about__nameInner">不可意思 / fkxsh</span></p>
          <p class="about__text" v-scroll="onScrollShow"><span class="about__textInner">どちらも ふかいし と読みます。</span></p>
          <p class="about__text" v-scroll="onScrollShow"><span class="about__textInner">本業はWebエンジニアをやっていて趣味でデザインも少しやります。</span></p>
          <p class="about__text" v-scroll="onScrollShow"><span class="about__textInner">アニメ・漫画・酒・服・サウナ・アーセナルなどが好きです。</span></p>
          <p class="about__text" v-scroll="onScrollShow"><span class="about__textInner">何かあればTwitterのDMに連絡ください。</span></p>
          <ul class="about__lists" v-scroll="onScrollShow">
            <li class="about__list" v-scroll="onScrollShow"><a class="about__listAnchor" href="https://twitter.com/fxkxxshx" target="_blank"><span class="about__listInner">Twitter</span></a></li>
            <li class="about__list" v-scroll="onScrollShow"><a class="about__listAnchor" href="https://github.com/fxkxxshx" target="_blank"><span class="about__listInner">GitHub</span></a></li>
            <li class="about__list" v-scroll="onScrollShow"><a class="about__listAnchor" href="https://zenn.dev/fxkxxshx" target="_blank"><span class="about__listInner">Zenn</span></a></li>
            <li class="about__list" v-scroll="onScrollShow"><a class="about__listAnchor" href="https://note.com/fxkxxshx" target="_blank"><span class="about__listInner">note</span></a></li>
          </ul>
        </article>
        <article class="about__item">
          <p class="about__name" v-scroll="onScrollShow"><span class="about__nameInner">FKXSHNUM</span></p>
          <p class="about__text" v-scroll="onScrollShow"><span class="about__textInner">同人イベント（同人誌即売会）にはこのサークル名で参加しています。</span></p>
          <p class="about__text" v-scroll="onScrollShow"><span class="about__textInner">直近はGSF03にて<a class="about__textAnchor" href="https://x.com/fxkxxshx/status/2070485375173472582" target="_blank"><span class="about__textAnchorInner">篠澤広モチーフのTシャツ</span></a>を頒布しました。</span></p>
        </article>
      </div>
    </section>
    <section class="work">
      <div class="work__left">
        <h2 class="work__title">
          <span class="work__titleText">works</span>
        </h2>
      </div>
      <div class="work__right" id="work">
        <article
          v-for="(workItem, index) in works"
          :key="workItem.href"
          class="work__item"
          :data-number="formatWorkNumber(index)"
        >
          <a class="work__itemAnchor" :href="workItem.href" target="_blank">
            <div class="work__itemRight" v-scroll="onScrollShow">
              <p class="work__name"><span class="work__nameInner">{{ workItem.name }}</span></p>
              <p class="work__text"><span class="work__textInner">{{ workItem.description }}</span></p>
            </div>
          </a>
        </article>
      </div>
    </section>
    <p class="qr">
      <span
        class="qr__image"
        :style="{
          maskImage: `url(${qrUrl})`,
          WebkitMaskImage: `url(${qrUrl})`
        }"
        aria-hidden="true"
      ></span>
    </p>
  </main>
</template>

<script>
import qrUrl from '@/assets/img/qr/qr.svg';

export default {
  name: 'Main',
  data () {
    return {
      qrUrl,
      works: [
        {
          name: 'TSUKIHI',
          description: '2024.02 / 自分が撮影した写真を掲載するWebサイト',
          href: 'https://tsukihi.fkxsh.com/'
        },
        {
          name: 'ネギャイベ2',
          description: '2023.04 / クラブイベントの告知Webサイト（現在は閉鎖中）',
          href: 'https://xn--ocke9cuax9kqe.com/'
        },
        {
          name: 'FKRK',
          description: '2023.04 / 自分が体験したモノやコトを記録するWebサイト',
          href: 'https://fkrk.fkxsh.com/'
        },
        {
          name: 'TEKUBI HIKARU',
          description: '2022.07 / Apple Watchをペンライトとして使用できるようにするWebアプリケーション',
          href: 'https://tekubi-hikaru.netlify.app/'
        },
      ]
    };
  },
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
    formatWorkNumber (index) {
      return String(this.works.length - index).padStart(2, '0');
    },
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
      aspect-ratio: 320.43 / 47.058;
      color: $mainColor;
      container-type: inline-size;
      margin: 0;
      font-size: 0;
      line-height: 1;
      overflow: hidden;
      width: 100%;
      @include tb {
        position: sticky;
        top: 45vh;
      }
    }

    &__titleText {
      display: inline-block;
      font-size: calc(100cqw * 66 / 320.43);
      transform: scaleX(0.9712);
      transform-origin: left top;
      white-space: nowrap;
      @include rg-ew;
      line-height: calc(47.058 / 66);
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

        .about__textAnchor {
          color: $subColor;

          &::before {
            opacity: 1;
          }
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
    }

    &__listAnchor,
    &__textAnchor {
      color: transparent;
      position: relative;
      text-decoration: none;
      transition: color 0.4s ease-out 0.3s;

      &:hover {
        @include tb {

          &::before {
            background: transparent;
          }

          .about__listInner,
          .about__textAnchorInner {
            background: $inkColor;
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

    &__textAnchor {

      &::before {
          bottom: 1px;
      }
    }

    &__listInner,
    &__textAnchorInner {
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
      aspect-ratio: 320.324 / 52.049;
      color: $mainColor;
      container-type: inline-size;
      margin: 0;
      font-size: 0;
      line-height: 1;
      overflow: hidden;
      width: 100%;
      @include tb {
        position: sticky;
        top: 45vh;
      }
    }

    &__titleText {
      display: inline-block;
      font-size: calc(100cqw * 73 / 320.324);
      transform: scaleX(0.8168);
      transform-origin: left top;
      white-space: nowrap;
      @include rg-ew;
      line-height: calc(52.049 / 73);
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
        content: attr(data-number);
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
        display: inline-block;
        text-decoration: none;

        &:hover {
          @include tb {
            .work__nameInner {
              background: $inkColor;
            }

            .work__text {

              &:last-of-type {

                &::before {
                  background: $inkColor;
                }
              }
            }

            .work__textInner {
              background: $inkColor;
            }
          }
        }
      }

      &Right {
        transform-origin: left top;

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
        margin: -0.5px 0 0;
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

    &__image {
      background: $mainColor;
      display: block;
      height: 45px;
      mask-position: center;
      mask-repeat: no-repeat;
      mask-size: contain;
      transition: background 0.3s ease-out;
      width: 100%;
      @include tb {
        height: 50px;
      }
    }
  }
}
</style>
