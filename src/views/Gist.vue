<template>
    <div v-if="!loading">
        <h2>{{ data.description }}</h2>
        <table border="1" v-for="(file, name) in data.files">
            <tr>
                <th>{{ name }}</th>
            </tr>
            <tr>
                <td style="padding:8px">
                    <pre>{{ file.content }}</pre>
                </td>
            </tr>
        </table>
        <span>By {{ data.owner.login }}, updated at {{ new Date(data.updated_at).toLocaleString() }}</span>
    </div>
</template>

<script setup>
import { onMounted, ref } from 'vue';
import axios from 'axios';
import { useRoute } from 'vue-router';
const route = useRoute();

const loading = ref(true);
const data = ref(null);

onMounted(async () => {
    let res = await axios.get(`https://api.github.com/gists/${route.params['id']}`);
    data.value = res.data;
    loading.value = false;
});
</script>