<template>
    <nav class="bg-white text-gray-800 px-4 py-3 shadow-md border-b">
        <div class="max-w-7xl mx-auto flex justify-between items-center">
            <NuxtLink to="/" class="text-xl font-bold text-green-500"><span class="text-indigo-600">Tec</span>mise</NuxtLink>

            <div class="flex items-center gap-4">
                <NuxtLink to="/" class=" px-2 py-1 hover:bg-gray-100 rounded">Home</NuxtLink>
                <div class="relative" @mouseleave="aberto = false">
                    <button @click="aberto = !aberto"
                        class="flex items-center gap-1 px-2 py-1 hover:bg-gray-100 rounded">
                        Usuário
                    </button>

                    <div v-if="aberto" class="absolute right-0 top-full w-40 bg-white text-gray-800 shadow-md rounded z-50">
                        <NuxtLink to="/usuarios" class="block px-4 py-2 rounded hover:bg-gray-100" @click="aberto = false">
                            Visualizar
                        </NuxtLink>
                        <NuxtLink to="/usuarios/cadastrar" class="block px-4 py-2 rounded hover:bg-gray-100"
                            @click="aberto = false">
                            Cadastrar
                        </NuxtLink>
                    </div>
                </div>
            </div>
        </div>
    </nav>
</template>

<script setup lang="ts">
import { ref } from 'vue'

const aberto = ref(false)
</script>
