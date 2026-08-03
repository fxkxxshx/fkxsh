<template>
  <aside
    v-if="isEnabled"
    class="themeEditor"
    :class="{ 'is-collapsed': isCollapsed }"
    aria-label="テーマ調整"
  >
    <button
      class="themeEditor__toggle"
      type="button"
      :aria-expanded="String(!isCollapsed)"
      @click="isCollapsed = !isCollapsed"
    >
      <span class="themeEditor__toggleSwatch" :style="{ background: colors.primary }"></span>
      {{ isCollapsed ? 'Theme' : '閉じる' }}
    </button>

    <div v-if="!isCollapsed" class="themeEditor__body">
      <div class="themeEditor__heading">
        <div>
          <p class="themeEditor__eyebrow">LIVE PREVIEW</p>
          <h2 class="themeEditor__title">Theme editor</h2>
        </div>
        <button class="themeEditor__reset" type="button" @click="reset">初期化</button>
      </div>

      <label
        v-for="field in fields"
        :key="field.key"
        class="themeEditor__field"
      >
        <span class="themeEditor__label">{{ field.label }}</span>
        <span class="themeEditor__controls">
          <input
            v-model="colors[field.key]"
            class="themeEditor__picker"
            type="color"
            :aria-label="`${field.label}のカラーピッカー`"
            @input="applyTheme"
          >
          <input
            :value="colors[field.key]"
            class="themeEditor__hex"
            type="text"
            maxlength="7"
            spellcheck="false"
            :aria-label="`${field.label}のHEX値`"
            @input="updateHex(field.key, $event.target.value)"
          >
        </span>
      </label>

      <div class="themeEditor__contrast">
        <p class="themeEditor__contrastTitle">Contrast on background</p>
        <p class="themeEditor__contrastRow">
          <span>Primary</span>
          <strong>{{ primaryContrast }}:1</strong>
          <span class="themeEditor__badge" :class="{ 'is-pass': primaryContrast >= 4.5 }">
            {{ primaryContrast >= 4.5 ? 'AA' : 'Low' }}
          </span>
        </p>
        <p class="themeEditor__contrastRow">
          <span>Text / hover</span>
          <strong>{{ inkContrast }}:1</strong>
          <span class="themeEditor__badge" :class="{ 'is-pass': inkContrast >= 4.5 }">
            {{ inkContrast >= 4.5 ? 'AA' : 'Low' }}
          </span>
        </p>
      </div>

      <button class="themeEditor__copy" type="button" @click="copyTheme">
        {{ copyLabel }}
      </button>
      <p class="themeEditor__note">変更内容はこのブラウザに自動保存されます。</p>
    </div>
  </aside>
</template>

<script>
const DEFAULT_COLORS = {
  primary: '#e00004',
  surface: '#f2f2f2',
  ink: '#000000'
};
const STORAGE_KEY = 'fkxsh-theme-editor';

export default {
  name: 'ThemeEditor',
  data () {
    return {
      colors: { ...DEFAULT_COLORS },
      fields: [
        { key: 'primary', label: 'Primary' },
        { key: 'surface', label: 'Background' },
        { key: 'ink', label: 'Text / hover' }
      ],
      isCollapsed: false,
      copyLabel: 'CSS変数をコピー'
    };
  },
  computed: {
    isEnabled () {
      return new URLSearchParams(window.location.search).get('theme-editor') === '1';
    },
    primaryContrast () {
      return this.contrastRatio(this.colors.primary, this.colors.surface);
    },
    inkContrast () {
      return this.contrastRatio(this.colors.ink, this.colors.surface);
    }
  },
  mounted () {
    if (!this.isEnabled) return;

    try {
      const saved = JSON.parse(localStorage.getItem(STORAGE_KEY));
      Object.keys(DEFAULT_COLORS).forEach((key) => {
        if (saved && this.isValidHex(saved[key])) {
          this.colors[key] = saved[key].toLowerCase();
        }
      });
    } catch (error) {
      localStorage.removeItem(STORAGE_KEY);
    }
    this.applyTheme();
  },
  methods: {
    isValidHex (value) {
      return /^#[0-9a-f]{6}$/i.test(value);
    },
    updateHex (key, value) {
      let normalized = value.trim();
      if (!normalized.startsWith('#')) normalized = `#${normalized}`;

      if (this.isValidHex(normalized)) {
        this.colors[key] = normalized.toLowerCase();
        this.applyTheme();
      }
    },
    applyTheme () {
      const root = document.documentElement;
      Object.entries(this.colors).forEach(([key, value]) => {
        root.style.setProperty(`--color-${key}`, value);
      });
      localStorage.setItem(STORAGE_KEY, JSON.stringify(this.colors));
      window.dispatchEvent(new CustomEvent('themechange', {
        detail: { ...this.colors }
      }));
    },
    reset () {
      this.colors = { ...DEFAULT_COLORS };
      this.applyTheme();
    },
    async writeClipboardText (text) {
      let clipboardError;

      if (navigator.clipboard && window.isSecureContext) {
        try {
          await navigator.clipboard.writeText(text);
          return;
        } catch (error) {
          clipboardError = error;
        }
      }

      const textarea = document.createElement('textarea');
      const activeElement = document.activeElement;
      textarea.value = text;
      textarea.setAttribute('readonly', '');
      textarea.style.position = 'fixed';
      textarea.style.opacity = '0';
      textarea.style.pointerEvents = 'none';
      document.body.appendChild(textarea);

      try {
        textarea.select();
        textarea.setSelectionRange(0, textarea.value.length);
        if (!document.execCommand('copy')) {
          throw clipboardError || new Error('Clipboard copy command was rejected');
        }
      } finally {
        textarea.remove();
        if (activeElement && typeof activeElement.focus === 'function') {
          activeElement.focus();
        }
      }
    },
    async copyTheme () {
      const css = `:root {
  --color-primary: ${this.colors.primary};
  --color-surface: ${this.colors.surface};
  --color-ink: ${this.colors.ink};
}`;
      try {
        await this.writeClipboardText(css);
        this.copyLabel = 'コピーしました';
      } catch (error) {
        // eslint-disable-next-line no-console
        console.error('Failed to copy theme CSS variables:', error);
        this.copyLabel = 'コピーできませんでした';
      }
      window.setTimeout(() => {
        this.copyLabel = 'CSS変数をコピー';
      }, 1800);
    },
    contrastRatio (foreground, background) {
      const luminance = (hex) => {
        const channels = hex.slice(1).match(/.{2}/g).map((value) => {
          const channel = parseInt(value, 16) / 255;
          return channel <= 0.03928
            ? channel / 12.92
            : Math.pow((channel + 0.055) / 1.055, 2.4);
        });
        return channels[0] * 0.2126 + channels[1] * 0.7152 + channels[2] * 0.0722;
      };
      const first = luminance(foreground);
      const second = luminance(background);
      return ((Math.max(first, second) + 0.05) / (Math.min(first, second) + 0.05)).toFixed(2);
    }
  }
};
</script>

<style scoped lang="scss">
.themeEditor {
  background: #111216;
  border: 1px solid rgba(255, 255, 255, 0.16);
  border-radius: 16px;
  bottom: 20px;
  box-shadow: 0 18px 50px rgba(0, 0, 0, 0.28);
  color: #f7f7f8;
  font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", sans-serif;
  position: fixed;
  right: 20px;
  width: min(300px, calc(100vw - 40px));
  z-index: 1000;

  &.is-collapsed {
    border-radius: 999px;
    width: auto;
  }

  &__toggle {
    align-items: center;
    background: transparent;
    border: 0;
    color: inherit;
    cursor: pointer;
    display: flex;
    font-size: 12px;
    font-weight: 700;
    gap: 8px;
    margin-left: auto;
    padding: 12px 14px;
  }

  &__toggleSwatch {
    border: 1px solid rgba(255, 255, 255, 0.5);
    border-radius: 50%;
    height: 10px;
    width: 10px;
  }

  &__body {
    padding: 0 18px 18px;
  }

  &__heading {
    align-items: flex-end;
    display: flex;
    justify-content: space-between;
    margin: 0 0 20px;
  }

  &__eyebrow {
    color: #9699a3;
    font-size: 9px;
    font-weight: 700;
    letter-spacing: 0.16em;
    margin: 0 0 4px;
  }

  &__title {
    font-size: 20px;
    line-height: 1;
    margin: 0;
  }

  &__reset {
    background: transparent;
    border: 0;
    color: #aeb0b8;
    cursor: pointer;
    font-size: 11px;
    padding: 0;
    text-decoration: underline;
  }

  &__field {
    display: block;
    margin: 0 0 14px;
  }

  &__label {
    color: #b8bac1;
    display: block;
    font-size: 11px;
    margin: 0 0 6px;
  }

  &__controls {
    display: flex;
    gap: 8px;
  }

  &__picker {
    background: transparent;
    border: 0;
    border-radius: 8px;
    cursor: pointer;
    height: 38px;
    padding: 0;
    width: 44px;
  }

  &__hex {
    background: #202126;
    border: 1px solid #34363d;
    border-radius: 8px;
    box-sizing: border-box;
    color: #ffffff;
    font-family: "SFMono-Regular", Consolas, monospace;
    font-size: 13px;
    height: 38px;
    padding: 0 10px;
    text-transform: uppercase;
    width: 100%;
  }

  &__contrast {
    border-bottom: 1px solid #2d2f35;
    border-top: 1px solid #2d2f35;
    margin: 18px 0;
    padding: 14px 0 10px;
  }

  &__contrastTitle {
    color: #8f929b;
    font-size: 10px;
    margin: 0 0 9px;
  }

  &__contrastRow {
    align-items: center;
    display: grid;
    font-size: 11px;
    grid-template-columns: 1fr auto 34px;
    gap: 8px;
    margin: 0 0 7px;
  }

  &__badge {
    background: #4b2a2c;
    border-radius: 999px;
    color: #ffb5b8;
    font-size: 9px;
    padding: 3px 0;
    text-align: center;

    &.is-pass {
      background: #183d2a;
      color: #8de4b1;
    }
  }

  &__copy {
    background: #ffffff;
    border: 0;
    border-radius: 9px;
    color: #111216;
    cursor: pointer;
    font-size: 12px;
    font-weight: 700;
    padding: 11px 12px;
    width: 100%;
  }

  &__note {
    color: #777a83;
    font-size: 9px;
    line-height: 1.5;
    margin: 9px 0 0;
    text-align: center;
  }
}
</style>
