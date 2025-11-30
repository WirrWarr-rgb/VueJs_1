<script setup>
import { ref } from 'vue'

const emit = defineEmits(['addTransaction'])

// Локальное состояние формы
const formData = ref({
  name: '',
  amount: '',
  type: 'income',
})

// Функция отправки формы
const handleSubmit = () => {
  const amountValue = Math.abs(Number(formData.value.amount.toString().replace(/-/g, '')))

  if (!formData.value.name.trim() || !amountValue || amountValue <= 0) {
    alert('Пожалуйста, заполните все поля корректно')
    return
  }

  emit('addTransaction', {
    name: formData.value.name.trim(),
    amount: amountValue,
    type: formData.value.type,
  })

  formData.value.name = ''
  formData.value.amount = ''
}
</script>

<template>
  <div
    class="bg-white dark:bg-gray-800 shadow rounded-xl p-4 flex flex-row gap-4 transition-colors duration-300"
  >
    <input
      v-model="formData.name"
      type="text"
      placeholder="Название"
      class="border border-gray-300 dark:border-gray-600 p-2 flex-1 rounded bg-white dark:bg-gray-700 text-gray-900 dark:text-gray-100 placeholder-gray-500 dark:placeholder-gray-400 transition-colors"
    />

    <input
      v-model="formData.amount"
      type="number"
      placeholder="Сумма"
      min="0"
      step="1"
      class="border border-gray-300 dark:border-gray-600 p-2 w-32 rounded bg-white dark:bg-gray-700 text-gray-900 dark:text-gray-100 placeholder-gray-500 dark:placeholder-gray-400 transition-colors"
    />

    <select
      v-model="formData.type"
      class="border border-gray-300 dark:border-gray-600 p-2 w-32 rounded bg-white dark:bg-gray-700 text-gray-900 dark:text-gray-100 transition-colors income-expense-select"
    >
      <option value="income" class="text-green-600 dark:text-green-400">Доход</option>
      <option value="expense" class="text-red-600 dark:text-red-400">Расход</option>
    </select>

    <button
      type="button"
      @click="handleSubmit"
      class="bg-blue-600 hover:bg-blue-700 dark:bg-blue-500 dark:hover:bg-blue-600 text-white px-4 py-2 transition-colors rounded"
    >
      Добавить
    </button>
  </div>
</template>

<style scoped>
.income-expense-select option {
  background-color: white;
  color: #1f2937;
}

.income-expense-select option[value='income'] {
  color: #16a34a;
}

.income-expense-select option[value='expense'] {
  color: #dc2626;
}

.dark .income-expense-select option {
  background-color: #374151;
  color: #f9fafb;
}

.dark .income-expense-select option[value='income'] {
  color: #4ade80; /* green-400 */
}

.dark .income-expense-select option[value='expense'] {
  color: #f87171; /* red-400 */
}
</style>
