<template>
  <div class="content">
    <select v-model="selectedVoice" class="border rounded-md p-2 mb-3 w-full">
      <option v-for="voice in voices" :key="voice.name" :value="voice.name">{{ voice.name }} ({{ voice.lang }})</option>
    </select>
    <textarea v-model="text" placeholder="Type something to speak..." rows="4"></textarea>
    <button @click="speak" class="bg-blue-500 text-white px-4 py-2 rounded-md hover:bg-blue-600">🔊 Speak</button>
  </div>
</template>

<script setup lang="ts">
import { ref } from 'vue';

const voices = ref<Array<SpeechSynthesisVoice>>([]);
const selectedVoice = ref<string>('');

const text = ref<string>('Hello, welcome to Vue text to speech!');

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
  min-height: 100vh;
  line-height: 1.1;
  text-align: center;
  flex-direction: column;
  justify-content: center;
}

.content h1 {
  font-size: 3.6rem;
  font-weight: 700;
}

.content p {
  font-size: 1.2rem;
  font-weight: 400;
  opacity: 0.5;
}
</style>
