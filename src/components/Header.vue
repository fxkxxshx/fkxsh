<template>
  <header class="header">
    <h1 class="header__logo">
      <a class="header__logoAnchor" href="#app" @click="onSmoothScroll">
      <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 72 18.185">
        <g transform="translate(-117.5 1208)">
          <path d="M396.91-1189.817H393.5L395.773-1208h3.41Z" transform="translate(-260.316 0.002)" fill="#0000ff"/>
          <path d="M126.621-1208a7.124,7.124,0,0,0-3.558.958,8.958,8.958,0,0,0-4.137,5.816l-.258,2.065-.032.253-.426,3.409-.71,5.683h3.41l.3-2.424.407-3.259.426-3.409h.028c0-.091,0-.182.006-.272l.151-1.209a6.024,6.024,0,0,1,1.33-2.7,4.082,4.082,0,0,1,3.059-1.5,4.082,4.082,0,0,1,3.06,1.5,6.323,6.323,0,0,1,1.487,4.184h3.41C134.577-1203.931,131.015-1208,126.621-1208Z" transform="translate(0 0.002)" fill="#0000ff"/>
          <g transform="translate(137.531 -1208)">
            <path d="M474.9-1194.725a6.321,6.321,0,0,1-1.486-4.184,6.323,6.323,0,0,1,1.486-4.184,4.083,4.083,0,0,1,3.06-1.5V-1208c-4.394,0-7.956,4.071-7.956,9.093s3.562,9.092,7.956,9.092v-3.41A4.081,4.081,0,0,1,474.9-1194.725Z" transform="translate(-470 1208.002)" fill="#0000ff"/>
          </g>
          <g transform="translate(154.295 -1208)">
            <path d="M769.9-1194.725a6.317,6.317,0,0,1-1.487-4.184,6.32,6.32,0,0,1,1.487-4.184,4.081,4.081,0,0,1,3.059-1.5V-1208c-4.394,0-7.956,4.071-7.956,9.093s3.562,9.092,7.956,9.092v-3.41A4.08,4.08,0,0,1,769.9-1194.725Z" transform="translate(-765 1208.002)" fill="#0000ff"/>
          </g>
          <g transform="translate(146.624 -1208)">
            <path d="M633.06-1203.093a6.323,6.323,0,0,1,1.487,4.184,6.32,6.32,0,0,1-1.487,4.184,4.082,4.082,0,0,1-3.06,1.5v3.41c4.394,0,7.957-4.07,7.957-9.092S634.394-1208,630-1208v3.41A4.083,4.083,0,0,1,633.06-1203.093Z" transform="translate(-630 1208.002)" fill="#0000ff"/>
          </g>
          <path d="M929.547-1198.909a6.321,6.321,0,0,1-1.487,4.184,4.081,4.081,0,0,1-3.059,1.5v3.41c4.394,0,7.955-4.07,7.955-9.092a6.324,6.324,0,0,1,1.487-4.184,4.084,4.084,0,0,1,3.06-1.5V-1208C933.109-1208,929.547-1203.931,929.547-1198.909Z" transform="translate(-761.613 0.002)" fill="#0000ff"/>
          <g transform="translate(173.588 -1208)">
            <path d="M1112.456-1198.909a7.12,7.12,0,0,0-3.527.941l1.254-10.033h-3.409l-1.954,15.627-.319,2.558h3.41a6.916,6.916,0,0,1,.218-1.737A5.969,5.969,0,0,1,1109.4-1194a4.083,4.083,0,0,1,3.06-1.5,4.084,4.084,0,0,1,3.06,1.5,6.321,6.321,0,0,1,1.486,4.184h3.41C1120.413-1194.839,1116.85-1198.909,1112.456-1198.909Z" transform="translate(-1104.501 1208.002)" fill="#0000ff"/>
          </g>
          <circle cx="2.841" cy="2.841" r="2.841" transform="translate(124.547 -1195.498)" fill="#0000ff"/>
        </g>
      </svg>
      </a>
    </h1>
    <ul class="header__lists">
      <li class="header__list" v-for="list in lists" :key="list[0]">
        <a class="header__listAnchor" :href="list[2]" @click="onSmoothScroll"><span class="header__listInner">{{list[1]}}</span>{{list[0]}}</a>
      </li>
    </ul>
    <p class="header__scroll" id="scroll">
      <a class="header__scrollAnchor" href="#about" @click="onSmoothScroll">
        <span class="header__scrollArrow">
          <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 5 3.116">
            <g transform="translate(-35 -599)">
              <path d="M-167.941,2041.153l-2.52-2.5.609-.614,1.905,1.892,1.871-1.89.615.608Z" transform="translate(205.461 -1439.038)" fill="#0000ff"/>
            </g>
          </svg>
        </span>
        <span class="header__scrollText">scroll</span>
      </a>
    </p>
  </header>
</template>

<script>
import 'scroll-behavior-polyfill';

export default {
  name: 'Header',
  data () {
    return {
      lists: [
        ['about', '(a)', '#about'],
        ['work', '(w)', '#work']
      ]
    };
  },
  mounted () {
    const target = document.getElementById('app');
    target.addEventListener('scroll', this.onScrollHide);
  },
  methods: {
    onScrollHide() {
      const scrollTarget = document.getElementById('scrollTarget');
      const top = scrollTarget.getBoundingClientRect().top;
      const scroll = document.getElementById('scroll');

      if (window.innerHeight > top) {
        scroll.classList.add('is-hide');
      } else {
        scroll.classList.remove('is-hide');
      }
    },
    onSmoothScroll (e) {
      e.preventDefault();
      const content = document.getElementById('app');
      const top = document.getElementById('main').getBoundingClientRect().top;
      const id = e.currentTarget.getAttribute('href');
      const targetElement = document.getElementById(id.replace('#', ''));
      const target = targetElement.getBoundingClientRect().top;
      if (target === 0) {
        content.scrollTo({
          top: 0,
          behavior: "smooth"
        });
      } else {
        content.scrollTo({
          top: target - top - 100,
          behavior: "smooth"
        });
      }
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="scss">
@import "@/assets/scss/var.scss";
@import "@/assets/scss/mixin.scss";

.header {
  &__logo {
    font-size: 0;
    left: 25px;
    margin: 0;
    position: fixed;
    top: 25px;
    width: 72px;
    z-index: 100;
    @include tb {
      left: calc(144 / 1440 * 100%);
      top: 45px;
      width: 100px;
    }

    &Anchor {
      
      &:hover {
        @include tb {
          
          path, circle {
            fill: #000;
          }
        }
      }
    }

    svg {
      width: 100%;

      path, circle {
        transition: fill 0.3s ease-out;
      }
    }
  }

  &__lists {
    display: flex;
    list-style: none;
    margin: 0;
    padding: 0;
    position: fixed;
    right: 25px;
    top: 25px;
    z-index: 100;
    @include rg-w_m;
    @include tb {
      right: calc(144 / 1440 * 100%);
      top: 45px;
      @include rg-w_m;
    }
  }

  &__list {

      & + .header__list {
        margin: 0 0 0 calc(20 / 375 * 100vw);
        @include tb {
          margin: 0 0 0 50px;
        }
      }

      &Anchor {
        color: $mainColor;
        display: inline-block;
        font-size: 13px;
        position: relative;
        text-decoration: none;
        transition: color 0.3s ease-out;
        @include tb {
          font-size: 18px;
        }

        &:hover {
          @include tb {
            color: #000;
          }
        }
      }

      &Inner {
          font-size: 10px;
          margin: 0 3px 0 0;
          vertical-align: top;
          @include rg-r;
          @include tb {
            font-size: 12px;
            margin: 0 5px 0 0;
          }
      }
  }

  &__scroll {
    bottom: 25px;
    filter: blur(0);
    height: 20px;
    left: 25px;
    margin: 0;
    opacity: 1;
    position: fixed;
    transition: opacity 0.5s ease-out, filter 0.5s ease-out, visibility 0.5s ease-out;
    visibility: visible;
    width: 64px;
    @include tb {
      bottom: 45px;
      height: 26px;
      left: calc(144 / 1440 * 100%);
      width: 84px;
    }

    &.is-hide {
      filter: blur(5px);
      opacity: 0;
      visibility: hidden;
    }

    &Anchor {
      align-items: center;
      border: 1px solid $mainColor;
      border-radius: 10px;
      box-sizing: border-box;
      display: flex;
      height: 100%;
      justify-content: center;
      text-decoration: none;
      transition: border 0.4s ease-out, background 0.4s ease-out;
      width: 100%;
      @include tb {
        border-radius: 13px;
      }

      &:hover, &:active {
        @include tb {
          border: 1px solid #000;
          background: #000;

          .header__scrollArrow {
            path {
              fill: $subColor;
            }
          }

          .header__scrollText {
            color: $subColor;
          }
        }
      }
    }

    &Arrow {
      font-size: 0;
      margin: 1px 3px 0 0;
      width: 5px;
      @include tb {
        margin: 1px 4px 0 0;
        width: 6px;
      }

      svg {
        width: 100%;

        path {
          transition: fill 0.4s ease-out;
        }
      }
    }

    &Text {
      color: $mainColor;
      font-size: 10px;
      margin: 1px 2px 0 0;
      transition: color 0.4s ease-out;
      @include rg-w_m;
      @include tb {
        font-size: 14px;
        margin: 1px 4px 0 0;
      }
    }
  }
}
</style>