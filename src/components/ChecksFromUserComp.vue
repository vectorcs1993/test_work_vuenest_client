<template>
    <button @click="create">+ Новый пользователь</button>
    <li v-for="(item) in users" :key="item">
        {{ item.name }}
    </li>
</template>
<script setup>
import {
    onMounted,
    ref
} from 'vue';
import axios from 'axios';

const users = ref([])


function update() {
    users.value.length = 0;
    axios.get('http://localhost:3000/users').then((res) => {
        users.value.push(...res.data);
    });
}
function create() {
    axios.post('http://localhost:3000/users', { name: 'Victor1' }).then(() => {
        update();
    });
}
onMounted(() => {
    update();
});

</script>