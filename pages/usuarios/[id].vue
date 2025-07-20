<template>
  <div class="flex justify-center items-center min-h-screen bg-gray-100">
    <div class="w-full max-w-lg bg-white rounded-2xl shadow-lg p-6">
      <div class="flex items-center justify-center p-4">
        <div class="mx-auto w-full max-w-[550px] bg-white">
          <form id="form">
            <div class="flex items-center justify-between mb-2">
              <div>
                <h2 class="text-2xl font-bold text-[#6A64F1]">Editar Usuário</h2>
              </div>
              <div class="text-right">
                <button type="button" @click="mostrarModal = true" title="Deletar"
                  class="hover:shadow-form rounded-md bg-[#DE3163] py-2 px-2 text-base font-semibold text-white outline-none mr-2">
                  <TrashIcon class="w-5 h-5" />
                </button>
                <NuxtLink to="/usuarios" title="Voltar">
                  <button type="button"
                    class="hover:shadow-form rounded-md bg-[#6A64F1] py-2 px-2 text-left text-base font-semibold text-white outline-none">
                    <ChevronLeftIcon class="w-5 h-5" />
                  </button>
                </NuxtLink>
              </div>
            </div>


            <div class="mb-3">
              <label for="name" class="mb-1 block text-base font-medium text-[#07074D]">
                Nome
              </label>
              <input type="text" name="nome" id="nome" v-model="form.nome" placeholder="Full Name"
                class="w-full rounded-md border border-[#e0e0e0] bg-white py-3 px-6 text-base font-medium text-[#6B7280] outline-none focus:border-[#6A64F1] focus:shadow-md" />
            </div>
            <div class="mb-3">
              <label for="phone" class="mb-1 block text-base font-medium text-[#07074D]">
                Telefone
              </label>
              <input type="text" name="telefone" id="telefone" @input="formatarTelefone" maxlength="15" minlength="15" v-model="form.telefone" placeholder="Enter your phone number"
                class="w-full rounded-md border border-[#e0e0e0] bg-white py-3 px-6 text-base font-medium text-[#6B7280] outline-none focus:border-[#6A64F1] focus:shadow-md" />
            </div>
            <div class="mb-3">
              <label for="email" class="mb-1 block text-base font-medium text-[#07074D]">
                Email
              </label>
              <input type="email" name="email" id="email" v-model="form.email" placeholder="Enter your email"
                class="w-full rounded-md border border-[#e0e0e0] bg-white py-3 px-6 text-base font-medium text-[#6B7280] outline-none focus:border-[#6A64F1] focus:shadow-md" />
            </div>
            <div class="mb-3">
              <label for="cpf" class="mb-1 block text-base font-medium text-[#07074D]">
                CPF
              </label>
              <input type="text" name="cpf" id="cpf" v-model="form.cpf" placeholder="Digite o CPF..." readonly
                class="w-full rounded-md border border-[#e0e0e0] bg-white py-3 px-6 text-base font-medium text-[#6B7280] outline-none focus:border-[#6A64F1] focus:shadow-md" />
            </div>
            <div class="-mx-3 flex flex-wrap">
              <div class="w-full px-3 sm:w-1/2">
                <div class="mb-5">
                  <label for="date" class="mb-1 block text-base font-medium text-[#07074D]">
                    Date
                  </label>
                  <input type="date" name="date" id="date" v-model="form.nascimento"
                    class="w-full rounded-md border border-[#e0e0e0] bg-white py-3 px-6 text-base font-medium text-[#6B7280] outline-none focus:border-[#6A64F1] focus:shadow-md" />
                </div>
              </div>
            </div>
            <div>
              <button
                type="button"
                title="Atualizar"
                @click="ConfirmUpdate = true"
                class="hover:shadow-form w-full rounded-md bg-[#6A64F1] py-3 px-8 text-center text-base font-semibold text-white outline-none">
                Atualizar
              </button>
            </div>
          </form>
        </div>
      </div>
    </div>
  </div>
  <div v-if="mostrarModal" class="fixed inset-0 bg-black bg-opacity-50 flex items-center justify-center z-50">
    <div class="bg-white p-6 rounded-lg shadow-lg max-w-sm w-full">
      <h2 class="text-lg font-bold mb-4 text-gray-800">Confirmar exclusão</h2>
      <p class="text-sm text-gray-600 mb-6">Tem certeza que deseja deletar este usuário?</p>
      <div class="flex justify-end gap-3">
        <button @click="mostrarModal = false" class="px-4 py-2 bg-gray-300 hover:bg-gray-400 rounded text-sm">
          Cancelar
        </button>
        <button @click="confirmarExclusao" class="px-4 py-2 bg-red-600 hover:bg-red-700 text-white rounded text-sm">
          Sim, deletar
        </button>
      </div>
    </div>
  </div>
  <div v-if="ConfirmUpdate" class="fixed inset-0 bg-black bg-opacity-50 flex items-center justify-center z-50">
    <div class="bg-white p-6 rounded-lg shadow-lg max-w-sm w-full">
      <h2 class="text-lg font-bold mb-4 text-gray-800">Confirmar Atualização</h2>
      <p class="text-sm text-gray-600 mb-6">Tem certeza que deseja atualizar este usuário?</p>
      <div class="flex justify-end gap-3">
        <button @click="ConfirmUpdate = false" class="px-4 py-2 bg-gray-300 hover:bg-gray-400 rounded text-sm">
          Cancelar
        </button>
        <button @click="atualizarUsuario" class="px-4 py-2 bg-[#6A64F1] hover:bg-red-700 text-white rounded text-sm">
          Sim, Atualizar
        </button>
      </div>
    </div>
  </div>

</template>

<script setup lang="ts">
import { ChevronLeftIcon } from '@heroicons/vue/24/solid'
import { TrashIcon } from '@heroicons/vue/24/solid'
import { useRoute } from 'vue-router'
import { computed } from 'vue'

type Usuario = {
  nome: string
  email: string
  cpf: string
  nascimento: string
  telefone: string
}

const form = ref({
  nome: '',
  email: '',
  cpf: '',
  nascimento: '',
  telefone: ''
})

const mostrarModal = ref(false)
const ConfirmUpdate = ref(false)
const route = useRoute()
const cpf = route.params.id as string

const { data, error } = await useFetch<Usuario[]>(
  () => `http://localhost:8080/usuarios/buscar?cpf=${cpf}`,
  {
    method: 'GET',
    key: `usuario-${cpf}`,
    immediate: true
  }
)

const usuario = computed(() => data.value?.[0])

watchEffect(() => {
  if (usuario.value) {
    const nascimentoFormatado =
      usuario.value.nascimento?.split('T')[0] ?? ''

    form.value = {
      ...usuario.value,
      nascimento: nascimentoFormatado
    }
  }
})

async function confirmarExclusao() {
  console.log('Confirmou exclusão')

  try {
    const { error } = await useFetch(() => `http://localhost:8080/usuarios/delete?cpf=${cpf}`, {
      method: 'DELETE',
    })

    if (error.value) {
      console.error('Erro ao excluir usuário:', error.value)
      alert('Erro ao excluir usuário')
      return
    }

    alert('Usuário excluído com sucesso')
    mostrarModal.value = false
    navigateTo('/usuarios')

  } catch (err) {
    console.error('Erro inesperado:', err)
    alert('Erro inesperado ao excluir')
  }
}

async function atualizarUsuario() {
  try {
    const { error } = await useFetch(() => `http://localhost:8080/usuarios/update?cpf=${cpf}`, {
      method: 'PUT',
      body: {
        nome: form.value.nome,
        email: form.value.email,
        nascimento: form.value.nascimento,
        telefone: form.value.telefone,
      }
    })

    if (error.value) {
      console.error(error.value)
    } else {
      alert('Usuário atualizado com sucesso')
      console.log('Usuário atualizado com sucesso!')
      // Você pode redirecionar o usuário para a página de listagem de usuários aqui
      // navigateTo('/usuarios')
    }
  } catch (error) {
    alert('Erro ao atualizar usuário')
    console.error(error)
  }
  ConfirmUpdate.value = false
  navigateTo(`/usuarios/${cpf}`)
}

function formatarTelefone(e: Event) {
  const input = e.target as HTMLInputElement
  let value = input.value.replace(/\D/g, '') // remove tudo que não for número

  if (value.length > 11) value = value.slice(0, 11)

  // Aplica máscara: (XX) XXXXX-XXXX
  if (value.length > 10) {
    value = value.replace(/(\d{2})(\d{5})(\d{4})/, '($1) $2-$3')
  } else if (value.length > 6) {
    value = value.replace(/(\d{2})(\d{4})(\d{0,4})/, '($1) $2-$3')
  } else if (value.length > 2) {
    value = value.replace(/(\d{2})(\d{0,5})/, '($1) $2')
  } else {
    value = value
  }

  form.value.telefone = value
}


</script>
