<script setup>
import BudgetSummary from './BudgetSummary.vue'
</script>

<template>
  <div class="bg-white shadow rounded-xl p-4">
    <h2 class="text-lg font-semibold mb-3">Операции</h2>

    <table class="w-full text-left border-collapse">
      <thead>
        <tr class="border-b">
          <th class="p-2">Название</th>
          <th class="p-2">Тип</th>
          <th class="p-2">Сумма</th>
          <th class="p-2">Действия</th>
        </tr>
      </thead>
      <tbody>
        <!-- пример статичных строк, у вас тоже должен быть + и - -->
        <tr class="border-b">
          <td class="p-2">Зарплата</td>
          <td class="p-2 text-green-600 font-medium">Доход</td>
          <td class="p-2">+50000</td>
          <td class="p-2 text-sm text-red-500 cursor-pointer">Удалить</td>
        </tr>
        <tr class="border-b">
          <td class="p-2">Еда</td>
          <td class="p-2 text-red-600 font-medium">Расход</td>
          <td class="p-2">-800</td>
          <td class="p-2 text-sm text-red-500 cursor-pointer">Удалить</td>
        </tr>
      </tbody>
    </table>
  </div>

  <BudgetSummary />
</template>
