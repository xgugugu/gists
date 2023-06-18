<template>
  <h2>Log in your github account.</h2>
  <strong>Token:</strong> Bearer <input type="text" v-model="token" />
  <button @click="submit">Submit</button><br>
  <span v-if="failed_verify" style="color:brown;font-size:small">failed to verify your token</span>
  <br><br>
  <span style="font-size:small">Click <a href="https://github.com/settings/tokens">here</a> to generate a new token</span>
</template>

<script setup>
import { ref } from 'vue';
import axios from 'axios';
import { useRouter } from 'vue-router';
const router = useRouter();

const token = ref('');
const failed_verify = ref(false);
function submit() {
  axios.get('https://api.github.com/gists', {
    headers: {
      'Authorization': `Bearer ${token.value}`
    }
  }).then((res) => {
    if (res.status == 200) {
      localStorage.setItem('Bearer-Token', token.value);
      router.push('/');
    } else {
      failed_verify.value = true;
    }
  }).catch(() => {
    failed_verify.value = true;
  });
}
</script>