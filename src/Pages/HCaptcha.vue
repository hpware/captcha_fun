<script setup>
// Import
import VueHcaptcha from '@hcaptcha/vue3-hcaptcha';
import cookies from 'vue-cookies';
import { ref, onUnmounted } from 'vue';
const hardnesskey = ref('');
const score = ref(0);
if (cookies.get('score')) {
  score.value = Math.round(cookies.get('score'));
}
const time = ref(0);
if (cookies.get('time')) {
  time.value = Math.round(cookies.get('time'));
}
const captchaRef = ref(null);
// Import Keys
const hardkey = import.meta.env.VITE_hCaptcha_Site_Key_Hard;
const modkey = import.meta.env.VITE_hCaptcha_Site_Key_Moderate;
const easykey = import.meta.env.VITE_hCaptcha_Site_Key_Easy;
const hardness = new URLSearchParams(window.location.search).get("hns");
const selected = ref(hardness);
if (!hardness) {
  selected.value = 'no';
}
if (['easy', 'mod', 'hard'].includes(hardness)) {
  selected.value = 'yes';
  if (hardness === 'easy') {
    hardnesskey.value = easykey;
  } else if (hardness === 'mod') {
    hardnesskey.value = modkey;
  } else if (hardness === 'hard') {
    hardnesskey.value = hardkey;
  }
}
if (selected.value === 'no') {
  cookies.remove('score');
  cookies.remove('time');
} else if (selected.value === 'yes') {
  const interval = setInterval(() => {
    time.value = Math.floor(time.value + 1);
    cookies.set('time', time.value);
    console.log(time.value);
  }, 1000);
  onUnmounted(() => {
      clearInterval(interval);
  });
}

// Captcha
const onCaptchaVerified = () => {
  score.value = Math.floor(score.value + 1);
  cookies.set('score', score.value);
  captchaRef.value.resetCaptcha(true);

};
const onCaptchaReset = () => {
  score.value = Math.floor(score.value - 10);
  cookies.set('score', score.value);
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
    <h3>Timer: <span id="timer">{{ time }}s</span><br>Score: <span id="score">{{ score }}</span></h3>
    <p style="color: red; font-size: 0.9em">Resets wiil -10 points</p>
  <vue-hcaptcha ref="captchaRef" :sitekey="hardnesskey" @verify="onCaptchaVerified" @reset="onCaptchaReset"></vue-hcaptcha>

  </div>
</template>

<style scoped>

</style>