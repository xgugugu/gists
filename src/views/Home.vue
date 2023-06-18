<template>
  <div v-if="is_loading"></div>
  <div v-else-if="is_login">
    <h2>Your Gists</h2>
    <table border="1">
      <tr>
        <th>Description</th>
        <th>Update at</th>
        <th>Link</th>
        <th>Do</th>
      </tr>
      <tr v-for="gist in data">
        <td>
          {{ gist.description }}
        </td>
        <td>
          {{ new Date(gist.updated_at).toLocaleString() }}
        </td>
        <td>
          <RouterLink :to="`/gist/${gist.id}`">link</RouterLink>
        </td>
        <td>
          <button @click="deleteGist(gist.id)">Delete</button>
        </td>
      </tr>
    </table>
    <RouterLink to="/create">Create new gist</RouterLink>
  </div>
  <div v-else>
    <h2>You are not logged in.</h2>
    <span>Click <RouterLink to="/login">here</RouterLink> to login.</span>
  </div>
</template>

<script setup>
import { ref } from 'vue';
import { Axios } from 'axios';
const axios = new Axios({
  baseURL: 'https://api.github.com',
  headers: {
    'Authorization': `Bearer ${localStorage.getItem('Bearer-Token')}`
  }
});

const is_loading = ref(true);
const is_login = ref(false);
const data = ref([]);

axios.get('/gists').then((res) => {
  if (res.status == 200) {
    data.value = JSON.parse(res.data);
    is_login.value = true;
  }
}).catch(() => {
  is_login.value = false;
}).finally(() => {
  is_loading.value = false;
});

function deleteGist(id) {
  if (confirm('确认删除？')) {
    axios.delete(`/gists/${id}`).catch(() => null).finally(() => {
      location.reload();
    });
  }
}
</script>
