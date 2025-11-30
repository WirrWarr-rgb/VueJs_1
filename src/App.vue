<script setup>
import { ref, watch, onMounted } from 'vue'
import BudgetForm from './components/BudgetForm.vue'
import BudgetTable from './components/BudgetTable.vue'

// Реактивное состояние для транзакций
const transactions = ref([])

// Состояние для темы
const isDarkMode = ref(false)

// Функция для добавления новой транзакции
const addTransaction = (transactionData) => {
  const newTransaction = {
    id: Date.now(),
    ...transactionData,
  }
  transactions.value.push(newTransaction)
}

// Функция для удаления одной транзакции
const deleteTransaction = (id) => {
  transactions.value = transactions.value.filter((transaction) => transaction.id !== id)
}

// Функция для обновления транзакции
const updateTransaction = (updatedTransaction) => {
  const index = transactions.value.findIndex((t) => t.id === updatedTransaction.id)
  if (index !== -1) {
    transactions.value[index] = { ...updatedTransaction }
  }
}

// Функция для удаления ВСЕХ транзакций
const deleteAllTransactions = () => {
  transactions.value = []
}

// Применяем класс темы к html элементу
watch(isDarkMode, (newValue) => {
  if (newValue) {
    document.documentElement.classList.add('dark')
  } else {
    document.documentElement.classList.remove('dark')
  }
  localStorage.setItem('darkMode', JSON.stringify(newValue))
})

// Сохранение в LocalStorage при изменении транзакций
watch(
  transactions,
  (newTransactions) => {
    localStorage.setItem('budgetTransactions', JSON.stringify(newTransactions))
  },
  { deep: true },
)

// Загрузка из LocalStorage при загрузке приложения
onMounted(() => {
  const savedTransactions = localStorage.getItem('budgetTransactions')
  if (savedTransactions) {
    transactions.value = JSON.parse(savedTransactions)
  }

  const savedTheme = localStorage.getItem('darkMode')
  if (savedTheme) {
    isDarkMode.value = JSON.parse(savedTheme)
    // Применяем тему сразу после загрузки
    if (isDarkMode.value) {
      document.documentElement.classList.add('dark')
    } else {
      document.documentElement.classList.remove('dark')
    }
  }
})
</script>

<template>
  <div :class="isDarkMode ? 'dark' : ''">
    <div
      class="min-h-screen bg-gray-50 dark:bg-gray-900 text-gray-900 dark:text-gray-100 p-6 transition-colors duration-300"
    >
      <div class="max-w-3xl mx-auto space-y-8">
        <div class="flex justify-center">
          <h1 class="text-3xl font-bold">Калькулятор бюджета</h1>
        </div>

        <BudgetForm @add-transaction="addTransaction" />
        <BudgetTable
          :transactions="transactions"
          @delete-transaction="deleteTransaction"
          @delete-all-transactions="deleteAllTransactions"
          @update-transaction="updateTransaction"
        />
      </div>
    </div>
  </div>
</template>
