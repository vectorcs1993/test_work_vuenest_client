<template>
    <li v-for="(item) in [{ id: 1, name: 'user1' }]" :key="item">
        {{ item.name }}
    </li>
</template>
<script setup>

</script>