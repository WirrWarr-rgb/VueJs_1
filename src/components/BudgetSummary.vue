<script setup>
import { computed } from 'vue'

const props = defineProps({
  transactions: {
    type: Array,
    required: true,
  },
})

// Функция для точных вычислений с округлением до 2 знаков
const preciseSum = (transactions) => {
  return transactions.reduce((sum, t) => {
    // Умножаем на 100, складываем как целые числа, затем делим на 100
    return Math.round((sum + t.amount) * 100) / 100
  }, 0)
}

// Функция для форматирования числа с фиксированным количеством знаков
const formatNumber = (num) => {
  return Math.round(num * 100) / 100 // Округляем до 2 знаков после запятой
}

// Вычисляемые значения
const totalIncome = computed(() => {
  const incomeTransactions = props.transactions.filter((t) => t.type === 'income')
  return formatNumber(preciseSum(incomeTransactions))
})

const totalExpenses = computed(() => {
  const expenseTransactions = props.transactions.filter((t) => t.type === 'expense')
  return formatNumber(preciseSum(expenseTransactions))
})

const balance = computed(() => {
  return formatNumber(totalIncome.value - totalExpenses.value)
})

const isBalanceNegative = computed(() => {
  return balance.value < 0
})
</script>

<template>
  <div
    class="bg-white dark:bg-gray-800 shadow rounded-xl p-4 text-center transition-colors duration-300"
  >
    <h2 class="text-lg font-semibold mb-3">Сводка</h2>

    <div class="grid grid-cols-3 gap-4">
      <div>
        <p class="text-gray-500 dark:text-gray-400">Доходы</p>
        <p class="text-green-600 dark:text-green-400 text-xl font-semibold">
          +{{ totalIncome.toFixed(2) }}
        </p>
      </div>
      <div>
        <p class="text-gray-500 dark:text-gray-400">Расходы</p>
        <p class="text-red-600 dark:text-red-400 text-xl font-semibold">
          -{{ totalExpenses.toFixed(2) }}
        </p>
      </div>
      <div>
        <p class="text-gray-500 dark:text-gray-400">Баланс</p>
        <p
          class="text-xl font-semibold"
          :class="
            balance >= 0 ? 'text-gray-800 dark:text-gray-200' : 'text-red-600 dark:text-red-400'
          "
        >
          {{ balance.toFixed(2) }}
        </p>
      </div>
    </div>

    <p v-if="isBalanceNegative" class="mt-4 text-red-500 dark:text-red-400 font-medium">
      ⚠️ Ваш баланс отрицательный!
    </p>
  </div>
</template>
