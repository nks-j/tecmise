<template>
  <div class="max-w-full mx-auto bg-zinc-100 min-h-screen pt-10">
    <div class="">
      <form @submit.prevent="cadastrarUsuario"
        class="max-w-md mx-auto flex flex-col gap-4 border-2 p-6 bg-white rounded-2xl shadow-md hover:shadow-blue-400 transition-shadow duration-200">
        <div class="flex items-center justify-between mb-2 ">
          <div>
            <p class="text-4xl font-bold text-indigo-500 text-center">Cadastrar</p>
          </div>
          <div class="text-right">
            <NuxtLink to="/usuarios" title="Voltar">
              <button type="button"
                class="hover:shadow-form rounded-md bg-[#6A64F1] py-2 px-2 text-left text-base font-semibold text-white outline-none">
                <ChevronLeftIcon class="w-5 h-5" />
              </button>
            </NuxtLink>
          </div>
        </div>
        <input v-model="novoUsuario.nome" type="text" placeholder="Nome" class="input p-2 rounded-md bg-zinc-100 border"
          required />
        <input v-model="novoUsuario.email" type="email" placeholder="Email"
          class="input p-2 rounded-md bg-zinc-100 border" required />
        <input v-model="novoUsuario.cpf" type="text" @input="formatarCPF" maxlength="14" minlength="14"
          placeholder="CPF" class="input p-2 rounded-md bg-zinc-100 border" required />
        <input v-model="novoUsuario.nascimento" type="date" class="input p-2 rounded-md bg-zinc-100 border" required />
        <input v-model="novoUsuario.telefone" type="text" @input="formatarTelefone" maxlength="15" minlength="15"
          placeholder="Telefone" class="input p-2 rounded-md bg-zinc-100 border" />
        <button type="submit" class="col-span-1 md:col-span-2 bg-blue-600 hover:bg-blue-700 text-white py-2 rounded">
          Cadastrar
        </button>
        <div v-if="resposta === 'Cadastro realizado'" class="text-center text-green-600 font-semibold">
          Cadastro realizado com sucesso!
        </div>
        <div v-else-if="resposta === 'Usuário já cadastrado'" class="text-center text-red-600 font-semibold">
          Usuário já cadastrado.
        </div>
        <div v-else-if="resposta === 'False'" class="text-center text-red-600 font-semibold">
          Erro ao cadastrar usuário.
        </div>
      </form>
    </div>
  </div>
</template>

<script lang="ts" setup>
import { watch } from 'vue'
import { ChevronLeftIcon } from '@heroicons/vue/24/solid'

type Usuario = {
  nome: string
  email: string
  cpf: string
  nascimento: Date
  telefone: string
}

const resposta = ref('')
const resposta1 = ref(false)

const novoUsuario = ref<Omit<Usuario, 'id'>>({
  nome: '',
  email: '',
  cpf: '',
  nascimento: new Date(),
  telefone: ''
})

const cadastrarUsuario = async () => {
  if (resposta1.value === true) {

    const res = await fetch('http://localhost:8080/usuarios/cadastrar', {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json'
      },
      body: JSON.stringify(novoUsuario.value)
    })

    if (res.ok) {
      alert("Cadastro realizado com sucesso")
      resposta.value = "Cadastro realizado"
      novoUsuario.value = {
        nome: '',
        email: '',
        cpf: '',
        nascimento: new Date(),
        telefone: ''
      }
    } else {
      resposta.value = "Erro ao cadastrar"
      const texto = await res.text()
      console.error(texto)
    }
  }
}



watch(() => novoUsuario.value.cpf, async (cpf) => {
  if (cpf.length !== 14) {
    resposta.value = ''
    resposta1.value = false
    return
  }

  const { data, error } = await useFetch<Usuario[]>(
    'http://localhost:8080/usuarios/buscar?cpf=' + cpf,
    {
      method: 'GET',
      key: 'usuarios-listar-' + cpf, // para evitar cache errado
      immediate: true
    }
  )


  if (error.value) {
    resposta.value = "Erro ao verificar usuário"
    resposta1.value = false
    console.error(error.value)
    return
  }

  if (data.value && data.value.length > 0) {
    resposta.value = "Usuário já cadastrado"
    resposta1.value = false
    console.log("Usuário já cadastrado")
  } else {
    resposta.value = "Usuário não encontrado"
    resposta1.value = true
    console.log("Usuário não encontrado, pode cadastrar")
  }
})

function formatarCPF(e: Event) {
  const input = e.target as HTMLInputElement
  let value = input.value.replace(/\D/g, '') // remove tudo que não for dígito

  if (value.length > 11) value = value.slice(0, 11)

  // Aplica a máscara XXX.XXX.XXX-XX
  if (value.length > 9) {
    value = value.replace(/(\d{3})(\d{3})(\d{3})(\d{2})/, '$1.$2.$3-$4')
  } else if (value.length > 6) {
    value = value.replace(/(\d{3})(\d{3})(\d{1,3})/, '$1.$2.$3')
  } else if (value.length > 3) {
    value = value.replace(/(\d{3})(\d{1,3})/, '$1.$2')
  }

  novoUsuario.value.cpf = value
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

  novoUsuario.value.telefone = value
}

</script>