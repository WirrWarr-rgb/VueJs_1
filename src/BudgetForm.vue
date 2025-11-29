<script setup></script>

<template>
  <div class="bg-white shadow rounded-xl p-4 flex flex-row gap-4">
    <input type="text" placeholder="Название" class="border p-2 flex-1" />

    <input type="number" placeholder="Сумма" class="border p-2 w-32" />

    <select class="border p-2 w-32">
      <option value="income">Доход</option>
      <option value="expense">Расход</option>
    </select>

    <button type="button" class="bg-blue-600 text-white px-4 py-2 hover:bg-blue-700 transition">
      Добавить
    </button>
  </div>
</template>
