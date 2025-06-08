<template lang="pug">
  #page-wrapper
    h1#newtab-name {{ $t('about.newtabPage.title') }}
    .select-ai-mode-box
      select.select-ai-mode-item(v-model="selectedAiMode")
        option.select-ai-mode-item-name(
          v-for="aiMode in $t('about.selectAiMode')"
          :key="aiMode.id"
          :value="aiMode.value"
        )
          | {{ aiMode.name }}
    .prompt-box
      input.prompt-input(
        type="text"
        v-model="searchQuery"
        @keyup.enter="onSearch"
        :placeholder="$t('about.newtabPage.searchPlaceholder')"
      )
</template>

<script lang="ts">
/* global Electron, Lulumi */
import { Component, Vue } from 'vue-property-decorator';

interface Window extends Lulumi.API.GlobalObject {
  ipcRenderer: Electron.IpcRenderer;
}

declare const window: Window;

@Component
export default class Newtab extends Vue {
  searchQuery = '';
  selectedAiMode = this.$t('about.selectAiMode')[0].value;

  mounted(): void {
    // Focus the input when the page loads
    const input = document.querySelector('.prompt-input') as HTMLInputElement;
    if (input) {
      input.focus();
    }
  }

  onSearch(): void {
    if (this.searchQuery.trim()) {
      window.ipcRenderer.send('new-tab', {
        url: this.searchQuery,
        follow: true,
      });
    }
  }
}
</script>

<style>
  #page-wrapper {
    width: 100vw;
    height: 100vh;
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    background-color: #161515;
  }

  #newtab-name {
    font-size: 2.5em;
    font-weight: 600;
    margin-bottom: 1em;
    background: linear-gradient(to right, #10a37f, #1a7f64);
    -webkit-background-clip: text;
    -webkit-text-fill-color: transparent;
  }

  .select-ai-mode-box {
    width: 10%;
    height: 30px;
    position: fixed;
    top: 10vh;
    left: 10%;
  }

  .select-ai-mode-item {
    line-height: 30px;
    width: 100%;
    height: 100%;
    background-color: transparent;
    color: #fff;
    border: none;
    outline: none;
  }

  .select-ai-mode-item-name {
    background-color: #161515;
    color: #fff;
  }

  .prompt-box {
    width: 100%;
    max-width: 800px;
    display: flex;
    align-items: center;
    justify-content: center;
    margin-top: 20px;
    position: fixed;
    bottom: 10vh;
  }

  .prompt-input {
    width: 100%;
    height: 80px;
    border: 1px solid #4a4a4a;
    border-radius: 12px;
    background-color: #333335;
    color: #fff;
    font-size: 16px;
    padding: 0 20px;
    outline: none;
    transition: border-color 0.2s;
  }

  .prompt-input:focus {
    border-color: #10a37f;
  }

  .prompt-input::placeholder {
    color: #8e8ea0;
  }
  </style>
