<template>
    <div class="container">
        <div v-if="users.length > 0" class="tables-container">
            <div class="users-section">
                <div class="container-header">
                    <h2>Пользователи</h2>
                    <form class="add-user" @submit.prevent="createUser">
                        <input class="styled-input" type="text" v-model="newUser.name" required placeholder="Имя" />
                        <button type="submit">Добавить</button>
                    </form>
                </div>
                <div class="table-wrapper">
                    <table class="user-table">
                        <thead>
                            <tr>
                                <th>Id</th>
                                <th>Имя</th>
                                <th>Кол-во чеков</th>
                                <th>Сумма чеков</th>
                                <th>Действия</th>
                            </tr>
                        </thead>
                        <tbody>
                            <tr v-for="user in users" :key="user.id" @click="selectUser(user.id)"
                                :class="{ 'selected': selectedUser?.id === user.id }">
                                <td>{{ user.id }}</td>
                                <td>{{ user.name }}</td>
                                <td>{{ user.checks_count }}</td>
                                <td>{{ user.checks_sum }}</td>
                                <td>
                                    <button @click.stop="deleteUser(user.id)">Удалить</button>
                                </td>
                            </tr>
                        </tbody>
                    </table>
                </div>
            </div>
            <div class="checks-section">
                <div class="container-header">
                    <h2>Чеки </h2>
                    <button v-if="selectedUser !== null" @click="dialogInputSum = true">Добавить чек</button>
                </div>
                <div class="table-wrapper">
                    <table class="user-table">
                        <thead>
                            <tr>
                                <th>Id</th>
                                <th>Сумма</th>
                            </tr>
                        </thead>
                        <tbody>
                            <tr v-for="check in checks" :key="check.id">
                                <td>{{ check.id }}</td>
                                <td>{{ check.sum }}</td>
                            </tr>
                        </tbody>
                    </table>
                </div>
            </div>
        </div>
        <p v-else>Пользователи не найдены.</p>
        <DialogInputComp v-model="dialogInputSum" :confirm="addCheck" />
    </div>
</template>

<script setup>
import { onMounted, ref } from 'vue';
import axios from 'axios';
import DialogInputComp from './DialogInputComp.vue';

const API_URL = 'http://localhost:3000';

const users = ref([]);
const checks = ref([]);
const selectedUser = ref(null);
const newUser = ref({ name: '' });
const dialogInputSum = ref(false);

function resetSelectUser() {
    selectedUser.value = null;
    checks.value = [];
}

function selectUser(id) {
    selectedUser.value = { id };
    axios.get(`${API_URL}/checks/from_user/${id}`).then((res) => {
        checks.value = res.data;
    });
}

function update() {
    axios.get(`${API_URL}/users/with_checks`).then((res) => {
        users.value = res.data;
        if (selectedUser.value !== null) {
            axios.get(`${API_URL}/checks/from_user/${selectedUser.value.id}`).then((res) => {
                checks.value = res.data;
            });
        }
    });
}

function createUser() {
    axios.post(`${API_URL}/users`, { name: newUser.value.name })
        .then((res) => {
            users.value.unshift({ id: res.data, name: newUser.value.name });
            newUser.value.name = '';
        });
}

function deleteUser(id) {
    if (confirm('Вы уверены, что хотите удалить пользователя?')) {
        axios.delete(`${API_URL}/users/${id}`).then(() => {
            if (selectedUser.value?.id === id) {
                resetSelectUser();
            }
            update();
        });
    }
}

function addCheck(sum) {
    axios.post(`${API_URL}/checks`, { user: selectedUser.value.id, sum })
        .then(() => {
            update();
        });
}

onMounted(update);
</script>

<style scoped>
.container {
    width: 90%;
    max-width: 1200px;
    margin: 0 auto;
    padding: 20px;
}

.tables-container {
    display: flex;
    gap: 20px;
    margin-top: 20px;
}

.users-section,
.checks-section {
    flex: 0.5;
    display: flex;
    flex-direction: column;
    border: 1px solid #e2e8f0;
    border-radius: 4px;
    overflow: hidden;
}

.table-wrapper {
    max-height: 500px;
    overflow-y: auto;
}

.user-table {
    width: 100%;
    border-collapse: collapse;
    font-family: Arial, sans-serif;
}

.user-table th {
    position: sticky;
    top: 0;
    text-align: center;
    background-color: #f5f7fa;
    padding: 12px 15px;
    font-weight: 600;
    border-bottom: 2px solid #e2e8f0;
    z-index: 1;
}

.user-table td {
    padding: 12px 15px;
    text-align: center;
    border-bottom: 1px solid #e2e8f0;
}

.user-table tr:not(.selected):hover {
    background-color: #f8fafc;
    cursor: pointer;
}

.user-table tr.selected {
    background-color: #ebf8ff;
    box-shadow: inset 3px 0 0 #4299e1;
}

.add-user {
    padding: 15px;
    display: flex;
    gap: 10px;
    background-color: #f8fafc;
    border-bottom: 1px solid #e2e8f0;
}

.container-header {
    padding: 15px;
    background-color: #f8fafc;
    border-bottom: 1px solid #e2e8f0;
}

/* Стили для скроллбара */
.table-wrapper::-webkit-scrollbar {
    width: 8px;
}

.table-wrapper::-webkit-scrollbar-track {
    background: #f1f1f1;
}

.table-wrapper::-webkit-scrollbar-thumb {
    background: #cbd5e0;
    border-radius: 4px;
}

.table-wrapper::-webkit-scrollbar-thumb:hover {
    background: #a0aec0;
}
</style>