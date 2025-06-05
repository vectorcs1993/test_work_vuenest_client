<template>
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

onMounted(() => {
    console.log(1);
    users.value.length = 0;
    axios.get('http://localhost:3000/users').then((res) => {
        users.value.push(...res.data);
    });
});

</script>