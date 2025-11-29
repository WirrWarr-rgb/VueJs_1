<script setup></script>

<template>
  <div class="bg-white shadow rounded-xl p-4 text-center">
    <h2 class="text-lg font-semibold mb-3">Сводка</h2>

    <div class="grid grid-cols-3 gap-4">
      <div>
        <p class="text-gray-500">Доходы</p>
        <p class="text-green-600 text-xl font-semibold">—</p>
      </div>
      <div>
        <p class="text-gray-500">Расходы</p>
        <p class="text-red-600 text-xl font-semibold">—</p>
      </div>
      <div>
        <p class="text-gray-500">Баланс</p>
        <p class="text-gray-800 text-xl font-semibold">—</p>
      </div>
    </div>

    <p class="mt-4 text-red-500 font-medium">⚠️ Ваш баланс отрицательный!</p>
  </div>
</template>
