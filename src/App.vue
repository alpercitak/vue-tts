<template>
  <div class="content">
    <n-select v-model:value="selectedVoice" :options="computedVoiceOptions" />
    <n-input v-model:value="text" type="textarea" />
    <n-button @click="speak" class="bg-blue-500 text-white px-4 py-2 rounded-md hover:bg-blue-600">🔊 Speak</n-button>
  </div>
</template>

<script setup lang="ts">
import { ref, computed } from 'vue';
import { NButton, NInput, NSelect } from 'naive-ui';

const voices = ref<Array<SpeechSynthesisVoice>>([]);
const selectedVoice = ref<string>('');
const text = ref<string>('Hello, welcome to Vue text to speech!');

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
</style>
