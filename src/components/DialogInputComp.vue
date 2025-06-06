<template>
  <div v-if="visible" class="dialog-overlay">
    <div class="dialog">
      <div class="dialog-header">
        <h3>{{ title }}</h3>
      </div>
      <div class="dialog-content">
        <input class="styled-input" type="number" v-model="sum" required placeholder="Сумма, руб" />
      </div>
      <div class="dialog-actions">
        <button class="dialog-button" @click.stop="visible = false">Отмена</button>
        <button class="dialog-button primary" @click="() => {
            visible = false;
            props.confirm(sum);
        }">Ок</button>
      </div>
    </div>
  </div>
</template>

<script setup>
import {
    defineModel,
    defineProps, 
    ref,
} from 'vue';

const visible = defineModel();

const props = defineProps({
  visible: Boolean,
  title: {
    type: String,
    default: 'Введите сумму чека'
  },
  confirm: Function,
});

const sum = ref(0);

</script>

<style scoped>
.dialog-overlay {
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background-color: rgba(0, 0, 0, 0.5);
  display: flex;
  align-items: center;
  justify-content: center;
  z-index: 1000;
}

.dialog {
  background-color: white;
  border-radius: 4px;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.15);
  width: 400px;
  max-width: 90%;
  overflow: hidden;
}

.dialog-header {
  padding: 16px;
  background-color: #f5f7fa;
  border-bottom: 1px solid #e2e8f0;
}

.dialog-header h3 {
  margin: 0;
  color: #2d3748;
  font-size: 1.1rem;
}

.dialog-content {
  padding: 20px 16px;
  color: #4a5568;
}

.dialog-actions {
  display: flex;
  justify-content: flex-end;
  gap: 8px;
  padding: 12px 16px;
  border-top: 1px solid #e2e8f0;
  background-color: #f8fafc;
}


</style>