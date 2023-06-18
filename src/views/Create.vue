<template>
    <h2>Create a gist</h2>
    Description: <input type="text" v-model="description" /> Public
    <button @click="submit">Submit</button><br>
    <span v-if="failed_post" style="color:brown;font-size:small">failed to create the gist</span>
    <table border="1">
        <tr>
            <th>Files <button @click="newFile">New</button></th>
        </tr>
        <tr v-for="(file, idx) in files">
            <td>
                <details style="margin:8px">
                    <summary>
                        <input type="text" v-model="file.name" />
                        <button @click="deleteFile(idx)">Delete</button>
                    </summary>
                    <textarea v-model="file.content"></textarea>
                </details>
            </td>
        </tr>
    </table>
</template>

<script setup>
import { ref } from 'vue';
import { Axios } from 'axios';
import { useRouter } from 'vue-router';
const router = useRouter();
const axios = new Axios({
    baseURL: 'https://api.github.com',
    headers: {
        'Authorization': `Bearer ${localStorage.getItem('Bearer-Token')}`
    }
});

const description = ref('');
const files = ref([]);
const failed_post = ref(false);

function newFile() {
    files.value.push({ name: 'filename', 'content': 'content' });
}
function deleteFile(idx) {
    files.value.splice(idx, 1);
}
function submit() {
    let json = {
        description: description.value,
        public: true,
        files: {}
    }
    for (let i in files.value) {
        let file = files.value[i];
        json.files[file.name] = { content: file.content };
    }
    axios.post('/gists', JSON.stringify(json)).then((res) => {
        if (res.status == 201) {
            router.push({ name: 'gist', params: { id: JSON.parse(res.data).id } });
        } else {
            failed_post.value = true;
        }
    })
}
</script>