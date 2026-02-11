<script lang="ts" setup>
import { NIcon, useThemeVars } from 'naive-ui';

import { RouterLink } from 'vue-router';
import { Heart, Home2, Menu2 } from '@vicons/tabler';

import { storeToRefs } from 'pinia';
import HeroGradient from '../assets/hero-gradient.svg?component';
import MenuLayout from '../components/MenuLayout.vue';
import NavbarButtons from '../components/NavbarButtons.vue';
import { useStyleStore } from '@/stores/style.store';
import { config } from '@/config';
import type { ToolCategory } from '@/tools/tools.types';
import { useToolStore } from '@/tools/tools.store';
import { useTracker } from '@/modules/tracker/tracker.services';
import CollapsibleToolMenu from '@/components/CollapsibleToolMenu.vue';

const themeVars = useThemeVars();
const styleStore = useStyleStore();
const version = config.app.version;
const commitSha = config.app.lastCommitSha.slice(0, 7);

const { tracker } = useTracker();
const { t } = useI18n();

const toolStore = useToolStore();
const { favoriteTools, toolsByCategory } = storeToRefs(toolStore);

const tools = computed<ToolCategory[]>(() => [
  ...(favoriteTools.value.length > 0 ? [{ name: t('tools.categories.favorite-tools'), components: favoriteTools.value }] : []),
  ...toolsByCategory.value,
]);
</script>

<template>
  <MenuLayout class="menu-layout" :class="{ isSmallScreen: styleStore.isSmallScreen }">
    <template #sider>
      <RouterLink to="/" class="hero-wrapper">
        <HeroGradient class="gradient" />
        <div class="text-wrapper">
          <div class="title">
            IT - TOOLS
          </div>
          <div class="divider" />
          <div class="subtitle">
            {{ $t('home.subtitle') }}
          </div>
        </div>
      </RouterLink>

      <div class="sider-content">
        <div v-if="styleStore.isSmallScreen" flex flex-col items-center>
          <locale-selector w="90%" />

          <div flex justify-center>
            <NavbarButtons />
          </div>
        </div>

        <CollapsibleToolMenu :tools-by-category="tools" />
      </div>
    </template>

    <template #content>
      <div flex items-center justify-between px-4 py-2 sticky top-0 z-20 bg-background op-95 backdrop-blur>
        <div flex items-center gap-2>
          <c-button
            circle
            variant="text"
            :aria-label="$t('home.toggleMenu')"
            @click="styleStore.isMenuCollapsed = !styleStore.isMenuCollapsed"
          >
            <NIcon size="24" :component="Menu2" />
          </c-button>

          <c-tooltip :tooltip="$t('home.home')" position="bottom">
            <c-button to="/" circle variant="text" :aria-label="$t('home.home')">
              <NIcon size="24" :component="Home2" />
            </c-button>
          </c-tooltip>

          <c-tooltip :tooltip="$t('home.uiLib')" position="bottom">
            <c-button v-if="config.app.env === 'development'" to="/c-lib" circle variant="text" :aria-label="$t('home.uiLib')">
              <icon-mdi:brush-variant text-20px />
            </c-button>
          </c-tooltip>
        </div>

        <div flex-1 max-w-600px mx-4>
          <command-palette />
        </div>

        <div flex items-center gap-2>
          <locale-selector v-if="!styleStore.isSmallScreen" />
          <NavbarButtons v-if="!styleStore.isSmallScreen" />
        </div>
      </div>
      <div class="main-content">
        <slot />

        <div class="footer-content mt-10 border-t border-neutral-200 dark:border-neutral-800 pt-8 pb-12">
          <div flex flex-wrap justify-between gap-8>
            <div max-w-300px>
              <div text-lg font-bold mb-4>IT-TOOLS</div>
              <div text-neutral-500 text-sm>
                {{ $t('home.subtitle') }}
              </div>
            </div>

            <div flex gap-12>
              <div>
                <div font-bold mb-4>{{ $t('home.footer.links') }}</div>
                <div flex flex-col gap-2 text-sm text-neutral-500>
                  <c-link href="https://github.com/mzlong/it-tools" target="_blank">GitHub</c-link>
                  <c-link href="https://x.com/ittoolsdottech" target="_blank">Twitter / X</c-link>
                </div>
              </div>
              <div>
                <div font-bold mb-4>{{ $t('home.footer.about') }}</div>
                <div flex flex-col gap-2 text-sm text-neutral-500>
                  <RouterLink to="/about" class="c-link">About</RouterLink>
                  <!-- <c-link href="https://dev.fktool.com" target="_blank">dev.fktool.com</c-link> -->
                </div>
              </div>
            </div>
          </div>

          <div mt-12 pt-8 border-t border-neutral-100 dark:border-neutral-900 text-center text-xs text-neutral-400>
            © {{ new Date().getFullYear() }} IT-TOOLS. Released under GPLv3 License.
            <div mt-2>
              Version: {{ version }} ({{ commitSha }})
            </div>
          </div>
        </div>
      </div>
    </template>
  </MenuLayout>
</template>

<style lang="less" scoped>
// ::v-deep(.n-layout-scroll-container) {
//     @percent: 4%;
//     @position: 25px;
//     @size: 50px;
//     @color: #eeeeee25;
//     background-image: radial-gradient(@color @percent, transparent @percent),
//         radial-gradient(@color @percent, transparent @percent);
//     background-position: 0 0, @position @position;
//     background-size: @size @size;
// }

.main-content {
  padding: 26px;
  flex: 1;

  @media (max-width: 640px) {
    padding: 16px;
  }
}

.support-button {
  background: rgb(37, 99, 108);
  background: linear-gradient(48deg, rgba(37, 99, 108, 1) 0%, rgba(59, 149, 111, 1) 60%, rgba(20, 160, 88, 1) 100%);
  color: #fff !important;
  transition: padding ease 0.2s !important;

  &:hover {
    color: #fff;
    padding-left: 30px;
    padding-right: 30px;
  }
}

.footer {
  text-align: center;
  color: #838587;
  margin-top: 20px;
  padding: 20px 0;
}

.sider-content {
  padding-top: 160px;
  padding-bottom: 200px;
}

.hero-wrapper {
  position: absolute;
  display: block;
  left: 0;
  width: 100%;
  z-index: 10;
  overflow: hidden;

  .gradient {
    margin-top: -65px;
  }

  .text-wrapper {
    position: absolute;
    left: 0;
    width: 100%;
    text-align: center;
    top: 16px;
    color: #fff;

    .title {
      font-size: 25px;
      font-weight: 600;
    }

    .divider {
      width: 50px;
      height: 2px;
      border-radius: 4px;
      background-color: v-bind('themeVars.primaryColor');
      margin: 0 auto 5px;
    }

    .subtitle {
      font-size: 16px;
    }
  }
}
</style>
