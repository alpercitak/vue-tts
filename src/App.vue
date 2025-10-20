<template>
  <div class="content">
    <div class="voice-selector">
      <n-select v-model:value="language" :options="languageOptions" filterable />
      <n-select v-model:value="voice" :options="voiceOptions" />
    </div>
    <n-input v-model:value="text" type="textarea" />
    <n-button @click="speak" class="bg-blue-500 text-white px-4 py-2 rounded-md hover:bg-blue-600">🔊 Speak</n-button>
  </div>
</template>

<script setup lang="ts">
import { ref, computed, watch } from 'vue';
import { NButton, NInput, NSelect } from 'naive-ui';
import type { SelectOptions } from './types';

const voices = ref<Array<SpeechSynthesisVoice>>([]);
const language = ref<string>('');
const voice = ref<string>('');
const text = ref<string>('Hello, welcome to Vue text to speech!');

const uniqueLanguageOptions = computed<Array<string>>(() =>
  Array.from(new Set(voices.value.map(({ lang }) => lang))).toSorted()
);
const languageOptions = computed<SelectOptions>(() =>
  uniqueLanguageOptions.value.map((lang) => ({ label: lang, value: lang }))
);
const voiceOptions = computed<SelectOptions>(() =>
  voices.value
    .filter(({ lang }) => lang === language.value)
    .map(({ name }) => ({ label: name, value: name }))
    .toSorted((a, b) => a.label.localeCompare(b.label))
);

window.speechSynthesis.onvoiceschanged = () => {
  voices.value = window.speechSynthesis.getVoices();
  if (voices.value.length) {
    language.value = voices.value[0].lang;
  }
};

watch(language, () => {
  voice.value = voiceOptions.value[0].value;
});

function speak() {
  if (!window.speechSynthesis) {
    alert('Sorry, your browser does not support Text-to-Speech.');
    return;
  }
  speechSynthesis.cancel();
  const utterance = new SpeechSynthesisUtterance(text.value);
  const selectedVoice = voices.value.find((v) => v.name === voice.value);
  if (selectedVoice) {
    utterance.voice = selectedVoice;
  }
  speechSynthesis.speak(utterance);
}
</script>

<style scoped>
.content {
  display: flex;
  gap: 1rem;
  padding: 1rem;
  flex-direction: column;
}
.voice-selector {
  display: flex;
  gap: 1rem;
}
</style>
