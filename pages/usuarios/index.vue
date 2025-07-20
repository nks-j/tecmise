<template>
  <div class="max-w-auto mx-auto p-4">
    <div class="max-w-6xl mx-auto p-6">
      <h1 class="text-3xl font-bold text-indigo-500 mb-3 left">Lista de Usuários</h1>
      <div class="flex items-center gap-4">
        <div class="relative">
          <button @click="open_filter = !open_filter"
            class="flex items-center gap-1 px-2 py-1 hover:bg-gray-100 rounded mb-2">
            Filtrar
            <AdjustmentsHorizontalIcon class="w-5 h-5" />
          </button>

          <div v-if="open_filter" class="mx-auto flex gap-4 items-center">
            <!-- Campo a ser filtrado -->
            <select v-model="campoSelecionado" class="w-32 px-5 py-2 border border-gray-300 rounded mb-2">
              <option value="cpf">CPF</option>
              <option value="email">Email</option>
            </select>

            <input id="valorBusca" v-model="valorSelecionado" list="valores-sugeridos"
              class="w-full px-6 py-2 border border-gray-300 rounded mb-2" />

            <datalist id="valores-sugeridos">
              <option v-for="valor in sugestoesDatalist" :value="valor" >
              </option>
            </datalist>
          </div>
        </div>
      </div>


      <div v-if="error && !open_filter" class="text-red-500 mb-4">Erro ao carregar usuários</div>
      <div v-else-if="pending && !open_filter" class="text-gray-500">Carregando...</div>

      <table v-else-if="data" class="w-full border border-gray-300 rounded shadow-md mb-4">
        <thead class="bg-zinc-100">
          <tr>
            <th class="p-2 text-center border-b">Nome</th>
            <th class="p-2 text-center border-b">Email</th>
            <th class="p-2 text-center border-b">CPF</th>
            <th class="p-2 text-center border-b">Nascimento</th>
            <th class="p-2 text-center border-b">Telefone</th>
            <th class="p-2 text-center border-b"></th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="usuario in usuariosFiltrados" :key="usuario.cpf" class="hover:bg-gray-50">
            <td class="p-2 text-center border-b">{{ usuario.nome }}</td>
            <td class="p-2 text-center border-b">{{ usuario.email }}</td>
            <td class="p-2 text-center border-b">{{ usuario.cpf }}</td>
            <td class="p-2 text-center border-b">{{ usuario.nascimento.toLocaleDateString('pt-BR') }}</td>
            <td class="p-2 text-center border-b">{{ usuario.telefone }}</td>
            <td class="p-2 text-center border-b">
              <NuxtLink :to="`/usuarios/${usuario.cpf}`" class="text-blue-600 hover:underline">
                Editar
              </NuxtLink>
            </td>
          </tr>
        </tbody>
      </table>
      <div class="mt-4 text-right mr-2">
        <NuxtLink to="/usuarios/cadastrar"
          class="inline-block bg-blue-600 text-white px-4 py-2 rounded hover:bg-blue-700 transition">
          Cadastrar
        </NuxtLink>
      </div>
    </div>
  </div>
</template>

<script lang="ts" setup>
import { AdjustmentsHorizontalIcon } from '@heroicons/vue/24/solid'
const open_filter = ref(false)
const campoSelecionado = ref('cpf')
const valorSelecionado = ref('')

type Usuario = {
  nome: string
  email: string
  cpf: string
  nascimento: Date
  telefone: string
}

const { data, status, error, refresh, pending } = await useFetch<Usuario[]>('http://localhost:8080/usuarios/lista', {
  method: 'GET',
  key: 'usuarios-listar',
  transform: (usuarios) =>
    usuarios.map(u => ({
      ...u,
      nascimento: new Date(u.nascimento) // ⬅️ converte string para Date de verdade
    })),
  immediate: true
})
const sugestoesDatalist = computed(() => {
  if (!data.value) return []
  return [...new Set(data.value.map(u => u[campoSelecionado.value as keyof Usuario]))]
})

const usuariosFiltrados = computed(() => {
  if (!data.value) return []

  if (!valorSelecionado.value) return data.value

  return data.value.filter((u) => {
    const campo = campoSelecionado.value as keyof Usuario
    const valorCampo = String(u[campo]).toLowerCase()
    const valorBusca = valorSelecionado.value.toLowerCase()

    return valorCampo.includes(valorBusca)
  })
})

console.log('Status:', status.value);
console.log('Data:', data.value);
</script>