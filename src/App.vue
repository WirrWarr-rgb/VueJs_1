<script setup>
import BudgetForm from './BudgetForm.vue'
import BudgetTable from './BudgetTable.vue'
</script>

<template>
  <div class="min-h-screen bg-gray-50 text-gray-900 p-6">
    <h1 class="text-3xl font-bold mb-6 text-center">Калькулятор бюджета</h1>

    <div class="max-w-3xl mx-auto space-y-8">
      <BudgetForm />
      <BudgetTable />
    </div>
  </div>
</template>
