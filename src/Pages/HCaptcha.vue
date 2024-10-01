<script setup>
// Import
import VueHcaptcha from '@hcaptcha/vue3-hcaptcha';
import { ref } from 'vue';
const hardnesskey = ref('');
const score = ref(0);
const time = ref(0);
// Import Keys
const hardkey = import.meta.env.VITE_hCaptcha_Site_Key_Hard;
const modkey = import.meta.env.VITE_hCaptcha_Site_Key_Moderate;
const easykey = import.meta.env.VITE_hCaptcha_Site_Key_Easy;
const hardness = new URLSearchParams(window.location.search).get("hns");
const selected = ref(hardness);
if (!hardness) {
  selected.value = 'no';
}
if (hardness === 'easy' | hardness === 'mod' | hardness === 'hard') {
  selected.value = 'yes';
  if (hardness === 'easy') {
    hardnesskey.value = easykey;
  } else if (hardness === 'mod') {
    hardnesskey.value = modkey;
  } else if (hardness === 'hard') {
    hardnesskey.value = hardkey;
  }
}
// Captcha
const onCaptchaVerified = (token) => {
  score.value = score.value + 1;
};

</script>

<template>
  <h2>HCaptcha</h2>
  <div v-if="selected === 'no'">
    <h3>Choose a difficulty</h3>
    <a href="/hcaptcha?hns=easy">Easy</a>&nbsp;
    <a href="/hcaptcha?hns=mod">Moderate</a>&nbsp;
    <a href="/hcaptcha?hns=hard">Hard</a>
  </div>
  <div v-if="selected === 'yes'">
    <h3>Timer: <span id="timer">{{ time }}</span><br>Score: <span id="score">{{ score }}</span></h3>
  <vue-hcaptcha sitekey="${{hardnesskey}}" @verify="onCaptchaVerified"></vue-hcaptcha>

  </div>
</template>

<style scoped>

</style>