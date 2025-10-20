<template>
  <div class="content">
    <div class="voice-selector">
      <n-select v-model:value="language" :options="languageOptions" filterable />
      <n-select v-model:value="selectedVoice" :options="computedVoiceOptions" />
    </div>
    <n-input v-model:value="text" type="textarea" />
    <n-button @click="speak" class="bg-blue-500 text-white px-4 py-2 rounded-md hover:bg-blue-600">🔊 Speak</n-button>
  </div>
</template>

<script setup lang="ts">
import { ref, computed } from 'vue';
import { NButton, NInput, NSelect } from 'naive-ui';
import type { SelectOptions } from './types';

const voices = ref<Array<SpeechSynthesisVoice>>([]);
const language = ref<string>('');
const selectedVoice = ref<string>('');
const text = ref<string>('Hello, welcome to Vue text to speech!');

const uniqueLanguageOptions = computed<Array<string>>(() =>
  Array.from(new Set(voices.value.map(({ lang }) => lang))).sort()
);
const languageOptions = computed<SelectOptions>(() =>
  uniqueLanguageOptions.value.map((lang) => ({ label: lang, value: lang }))
);
const computedVoiceOptions = computed<Array<{ label: string; value: string }>>(() =>
  voices.value.map((voice) => ({
    label: `${voice.name} (${voice.lang})`,
    value: voice.name,
  }))
);

window.speechSynthesis.onvoiceschanged = () => {
  voices.value = window.speechSynthesis.getVoices();
  if (voices.value.length && !selectedVoice.value) {
    selectedVoice.value = voices.value[0].name;
    language.value = voices.value[0].lang;
  }
};

function speak() {
  if (!window.speechSynthesis) {
    alert('Sorry, your browser does not support Text-to-Speech.');
    return;
  }
  const utterance = new SpeechSynthesisUtterance(text.value);
  const voice = voices.value.find((v) => v.name === selectedVoice.value);
  utterance.lang = 'en-US'; // Change to 'fr-FR', 'es-ES', etc. if needed
  if (voice) {
    utterance.voice = voice;
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
