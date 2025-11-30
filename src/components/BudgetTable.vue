<script setup>
import { ref, computed } from 'vue'
import BudgetSummary from './BudgetSummary.vue'

const props = defineProps({
  transactions: {
    type: Array,
    required: true,
  },
})

const emit = defineEmits(['deleteTransaction', 'deleteAllTransactions', 'updateTransaction'])

// Состояние для сортировки
const sortConfig = ref({
  field: 'amount',
  order: 'asc',
})

// Состояние для редактирования
const editingId = ref(null)
const editForm = ref({
  name: '',
  amount: '',
  type: 'income',
})

// Функция переключения сортировки
const toggleSort = (field) => {
  if (sortConfig.value.field === field) {
    sortConfig.value.order = sortConfig.value.order === 'asc' ? 'desc' : 'asc'
  } else {
    sortConfig.value.field = field
    sortConfig.value.order = 'asc'
  }
}

// Функция для получения символа сортировки
const getSortSymbol = (field) => {
  if (sortConfig.value.field !== field) return '↕'
  return sortConfig.value.order === 'asc' ? '↑' : '↓'
}

// Отсортированные транзакции
const sortedTransactions = computed(() => {
  return [...props.transactions].sort((a, b) => {
    let aValue, bValue

    switch (sortConfig.value.field) {
      case 'name': {
        aValue = a.name.toLowerCase()
        bValue = b.name.toLowerCase()
        break
      }
      case 'type': {
        aValue = a.type
        bValue = b.type
        break
      }
      case 'amount': {
        const aPriority = a.type === 'income' ? 1 : 0
        const bPriority = b.type === 'income' ? 1 : 0

        if (aPriority !== bPriority) {
          aValue = aPriority
          bValue = bPriority
        } else {
          aValue = Math.abs(a.amount)
          bValue = Math.abs(b.amount)
        }
        break
      }
      default:
        return 0
    }

    let result = 0
    if (aValue < bValue) result = -1
    if (aValue > bValue) result = 1

    return sortConfig.value.order === 'asc' ? result : -result
  })
})

// Функция начала редактирования
const startEdit = (transaction) => {
  editingId.value = transaction.id
  editForm.value = {
    name: transaction.name,
    amount: transaction.amount.toString(),
    type: transaction.type,
  }
}

// Функция сохранения редактирования
const saveEdit = () => {
  const amountValue = Math.abs(Number(editForm.value.amount.toString().replace(/-/g, '')))

  if (!editForm.value.name.trim() || !amountValue || amountValue <= 0) {
    alert('Пожалуйста, заполните все поля корректно')
    return
  }

  emit('updateTransaction', {
    id: editingId.value,
    name: editForm.value.name.trim(),
    amount: amountValue,
    type: editForm.value.type,
  })

  cancelEdit()
}

// Функция отмены редактирования
const cancelEdit = () => {
  editingId.value = null
  editForm.value = {
    name: '',
    amount: '',
    type: 'income',
  }
}

// Функция удаления одной транзакции
const handleDelete = (id) => {
  emit('deleteTransaction', id)
}

// Функция удаления всех транзакций
const handleDeleteAll = () => {
  if (props.transactions.length === 0) return

  if (confirm('Вы уверены, что хотите удалить ВСЕ записи? Это действие нельзя отменить')) {
    emit('deleteAllTransactions')
  }
}
</script>

<template>
  <div class="space-y-8">
    <div class="bg-white dark:bg-gray-800 shadow rounded-xl p-4 transition-colors duration-300">
      <div class="flex justify-between items-center mb-3">
        <h2 class="text-lg font-semibold">Операции</h2>
        <button
          v-if="transactions.length > 0"
          @click="handleDeleteAll"
          class="bg-red-500 hover:bg-red-600 dark:bg-red-600 dark:hover:bg-red-700 text-white px-3 py-1 rounded text-sm transition-colors"
        >
          Удалить все
        </button>
      </div>

      <table class="w-full text-left border-collapse table-fixed">
        <colgroup>
          <col class="w-2/5" />
          <!-- Название - 40% ширины -->
          <col class="w-1/5" />
          <!-- Тип - 20% ширины -->
          <col class="w-1/5" />
          <!-- Сумма - 20% ширины -->
          <col class="w-1/5" />
          <!-- Действия - 20% ширины -->
        </colgroup>
        <thead>
          <tr class="border-b border-gray-200 dark:border-gray-700">
            <th
              class="p-2 cursor-pointer hover:bg-gray-50 dark:hover:bg-gray-700 transition-colors"
              @click="toggleSort('name')"
            >
              Название
              <span class="text-xs ml-1">
                {{ getSortSymbol('name') }}
              </span>
            </th>
            <th
              class="p-2 cursor-pointer hover:bg-gray-50 dark:hover:bg-gray-700 transition-colors"
              @click="toggleSort('type')"
            >
              Тип
              <span class="text-xs ml-1">
                {{ getSortSymbol('type') }}
              </span>
            </th>
            <th
              class="p-2 cursor-pointer hover:bg-gray-50 dark:hover:bg-gray-700 transition-colors"
              @click="toggleSort('amount')"
            >
              Сумма
              <span class="text-xs ml-1">
                {{ getSortSymbol('amount') }}
              </span>
            </th>
            <th class="p-2">Действия</th>
          </tr>
        </thead>
        <tbody>
          <tr
            v-for="transaction in sortedTransactions"
            :key="transaction.id"
            class="border-b border-gray-200 dark:border-gray-700 hover:bg-gray-50 dark:hover:bg-gray-700 transition-colors"
          >
            <td class="p-2">
              <div v-if="editingId === transaction.id">
                <input
                  v-model="editForm.name"
                  type="text"
                  class="border border-gray-300 dark:border-gray-600 p-1 rounded w-full text-sm bg-white dark:bg-gray-700 text-gray-900 dark:text-gray-100 transition-colors"
                  @keyup.enter="saveEdit"
                  @keyup.escape="cancelEdit"
                />
              </div>
              <span v-else class="break-words">{{ transaction.name }}</span>
            </td>
            <td class="p-2">
              <div v-if="editingId === transaction.id">
                <select
                  v-model="editForm.type"
                  class="border border-gray-300 dark:border-gray-600 p-1 rounded w-full text-sm bg-white dark:bg-gray-700 text-gray-900 dark:text-gray-100 transition-colors"
                >
                  <option value="income">Доход</option>
                  <option value="expense">Расход</option>
                </select>
              </div>
              <span
                v-else
                class="font-medium"
                :class="
                  transaction.type === 'income'
                    ? 'text-green-600 dark:text-green-400'
                    : 'text-red-600 dark:text-red-400'
                "
              >
                {{ transaction.type === 'income' ? 'Доход' : 'Расход' }}
              </span>
            </td>
            <td class="p-2">
              <div v-if="editingId === transaction.id">
                <input
                  v-model="editForm.amount"
                  type="number"
                  min="0"
                  step="1"
                  class="border border-gray-300 dark:border-gray-600 p-1 rounded w-full text-sm bg-white dark:bg-gray-700 text-gray-900 dark:text-gray-100 transition-colors"
                  @keyup.enter="saveEdit"
                  @keyup.escape="cancelEdit"
                />
              </div>
              <span
                v-else
                class="font-medium"
                :class="
                  transaction.type === 'income'
                    ? 'text-green-600 dark:text-green-400'
                    : 'text-red-600 dark:text-red-400'
                "
              >
                {{ transaction.type === 'income' ? '+' : '-' }}{{ transaction.amount }}
              </span>
            </td>
            <td class="p-2">
              <div v-if="editingId === transaction.id" class="flex flex-col gap-1">
                <button
                  @click="saveEdit"
                  class="text-sm text-green-600 dark:text-green-400 cursor-pointer hover:text-green-800 dark:hover:text-green-300 transition-colors text-left"
                >
                  Сохранить
                </button>
                <button
                  @click="cancelEdit"
                  class="text-sm text-gray-600 dark:text-gray-400 cursor-pointer hover:text-gray-800 dark:hover:text-gray-300 transition-colors text-left"
                >
                  Отменить
                </button>
              </div>
              <div v-else class="flex flex-col gap-1">
                <button
                  @click="startEdit(transaction)"
                  class="text-sm text-blue-500 dark:text-blue-400 cursor-pointer hover:text-blue-700 dark:hover:text-blue-300 transition-colors text-left"
                >
                  Изменить
                </button>
                <button
                  @click="handleDelete(transaction.id)"
                  class="text-sm text-red-500 dark:text-red-400 cursor-pointer hover:text-red-700 dark:hover:text-red-300 transition-colors text-left"
                >
                  Удалить
                </button>
              </div>
            </td>
          </tr>
          <tr v-if="transactions.length === 0">
            <td colspan="4" class="p-4 text-center text-gray-500 dark:text-gray-400">
              Нет операций. Добавьте первую запись!
            </td>
          </tr>
        </tbody>
      </table>
    </div>

    <BudgetSummary :transactions="transactions" />
  </div>
</template>
