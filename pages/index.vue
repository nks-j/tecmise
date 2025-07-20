<template>
  <div class="min-h-screen bg-gradient-to-b from-indigo-100 to-white flex flex-col items-center justify-center text-center px-4">
    <h1 class="text-5xl md:text-6xl font-extrabold text-[#6A64F1] mb-4">
      Tecmise
    </h1>
    <p class="text-lg md:text-xl text-gray-700 mb-8 max-w-2xl">
      Sistema de gerenciamento de usuários simples, eficiente e rápido.
    </p>

    <div class="flex flex-col sm:flex-row gap-4">
      <NuxtLink to="/usuarios/cadastrar">
        <button class="px-6 py-3 bg-[#6A64F1] text-white rounded-lg font-semibold hover:bg-indigo-700 transition">
          Cadastrar Usuário
        </button>
      </NuxtLink>

      <NuxtLink to="/usuarios">
        <button class="px-6 py-3 bg-gray-200 text-gray-800 rounded-lg font-semibold hover:bg-gray-300 transition">
          Ver Lista de Usuários
        </button>
      </NuxtLink>
    </div>
  </div>
</template>
