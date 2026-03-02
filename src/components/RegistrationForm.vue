<template>
  <div class="w-full max-w-3xl bg-white rounded-2xl shadow-lg p-8">
    <div class="mb-8 text-center">
      <h1 class="text-3xl font-bold text-gray-900">Cadastro de Aplicativo</h1>
      <p class="mt-2 text-gray-500">Registre seu aplicativo para utilizar a API do Hub de Emergências Zona da Mata</p>
    </div>

    <form @submit.prevent="handleSubmit" novalidate class="space-y-4">
      <div class="grid grid-cols-1 sm:grid-cols-2 gap-4">
        <div>
          <label for="nome" class="block text-sm font-medium text-gray-700 mb-1">
            Nome do Aplicativo <span class="text-red-500">*</span>
          </label>
          <input
            id="nome"
            v-model="form.nome"
            type="text"
            placeholder="Meu App"
            :class="inputClass('nome')"
            @blur="touch('nome')"
          />
          <p v-if="errors.nome" class="mt-1 text-sm text-red-600">{{ errors.nome }}</p>
        </div>

        <div>
          <label for="email" class="block text-sm font-medium text-gray-700 mb-1">
            E-mail <span class="text-red-500">*</span>
          </label>
          <input
            id="email"
            v-model="form.email"
            type="email"
            placeholder="contato@exemplo.com"
            :class="inputClass('email')"
            @blur="touch('email')"
          />
          <p v-if="errors.email" class="mt-1 text-sm text-red-600">{{ errors.email }}</p>
        </div>
      </div>

      <div class="grid grid-cols-1 sm:grid-cols-2 gap-4">
        <div>
          <label for="celular" class="block text-sm font-medium text-gray-700 mb-1">
            Celular <span class="text-red-500">*</span>
          </label>
          <input
            id="celular"
            v-model="form.celular"
            type="tel"
            placeholder="(31) 99999-9999"
            :class="inputClass('celular')"
            @blur="touch('celular')"
            @input="maskPhone"
          />
          <p v-if="errors.celular" class="mt-1 text-sm text-red-600">{{ errors.celular }}</p>
        </div>

        <div>
          <label for="responsavel" class="block text-sm font-medium text-gray-700 mb-1">
            Responsável <span class="text-red-500">*</span>
          </label>
          <input
            id="responsavel"
            v-model="form.responsavel"
            type="text"
            placeholder="Nome do criador ou empresa"
            :class="inputClass('responsavel')"
            @blur="touch('responsavel')"
          />
          <p v-if="errors.responsavel" class="mt-1 text-sm text-red-600">{{ errors.responsavel }}</p>
        </div>
      </div>

      <div>
        <label for="url" class="block text-sm font-medium text-gray-700 mb-1">
          URL do Site <span class="text-red-500">*</span>
        </label>
        <input
          id="url"
          v-model="form.url"
          type="url"
          placeholder="https://meusite.com.br"
          :class="inputClass('url')"
          @blur="touch('url')"
        />
        <p v-if="errors.url" class="mt-1 text-sm text-red-600">{{ errors.url }}</p>
      </div>

      <div>
        <label class="block text-sm font-medium text-gray-700 mb-2">
          Tipo do Projeto <span class="text-red-500">*</span>
        </label>
        <div class="grid grid-cols-2 gap-3">
          <label
            :class="typeCardClass('private')"
            class="flex flex-col items-center gap-2 p-4 rounded-xl border-2 cursor-pointer transition-all"
          >
            <input
              v-model="form.tipo"
              type="radio"
              value="private"
              class="sr-only"
            />
            <svg class="w-6 h-6" fill="none" stroke="currentColor" viewBox="0 0 24 24">
              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2"
                d="M12 15v2m-6 4h12a2 2 0 002-2v-6a2 2 0 00-2-2H6a2 2 0 00-2 2v6a2 2 0 002 2zm10-10V7a4 4 0 00-8 0v4h8z" />
            </svg>
            <span class="font-semibold text-sm">Projeto privado</span>
          </label>

          <label
            :class="typeCardClass('public')"
            class="flex flex-col items-center gap-2 p-4 rounded-xl border-2 cursor-pointer transition-all"
          >
            <input
              v-model="form.tipo"
              type="radio"
              value="public"
              class="sr-only"
            />
            <svg class="w-6 h-6" fill="none" stroke="currentColor" viewBox="0 0 24 24">
              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2"
                d="M3.055 11H5a2 2 0 012 2v1a2 2 0 002 2 2 2 0 012 2v2.945M8 3.935V5.5A2.5 2.5 0 0010.5 8h.5a2 2 0 012 2 2 2 0 104 0 2 2 0 012-2h1.064M15 20.488V18a2 2 0 012-2h3.064" />
            </svg>
            <span class="font-semibold text-sm">Open Source</span>
          </label>
        </div>
      </div>

      <div>
        <label for="repositorio" class="block text-sm font-medium text-gray-700 mb-1">
          Repositório Git
          <span class="ml-1 text-xs text-gray-400 font-normal">(opcional)</span>
        </label>
        <input
          id="repositorio"
          v-model="form.repositorio"
          type="url"
          placeholder="https://github.com/usuario/repositorio"
          :class="inputClass('repositorio')"
          @blur="touch('repositorio')"
        />
        <p v-if="errors.repositorio" class="mt-1 text-sm text-red-600">{{ errors.repositorio }}</p>
      </div>

      <div>
        <label for="metadados" class="block text-sm font-medium text-gray-700 mb-1">
          Metadados
          <span class="ml-1 text-xs text-gray-400 font-normal">(opcional, formato JSON)</span>
        </label>
        <textarea
          id="metadados"
          v-model="form.metadados"
          rows="2"
          placeholder='{"versao": "1.0", "descricao": "..."}'
          :class="textareaClass()"
          @blur="touch('metadados')"
          class="font-mono text-sm"
        />
        <p v-if="errors.metadados" class="mt-1 text-sm text-red-600">{{ errors.metadados }}</p>
        <p class="mt-1 text-xs text-gray-400">
          Informações adicionais no formato JSON. Deixe em branco se não precisar.
        </p>
      </div>

      <div class="pt-2">
        <button
          type="submit"
          :disabled="isSubmitting"
          class="w-full py-3 px-6 rounded-xl font-semibold text-white bg-blue-600 hover:bg-blue-700 focus:outline-none focus:ring-4 focus:ring-blue-300 disabled:opacity-60 disabled:cursor-not-allowed transition-colors"
        >
          <span v-if="isSubmitting">Enviando...</span>
          <span v-else>Cadastrar Aplicativo</span>
        </button>
      </div>

      <div
        v-if="submitStatus === 'success'"
        class="p-4 rounded-xl bg-green-50 border border-green-200 text-green-800 text-sm"
      >
        Cadastro realizado com sucesso! Entraremos em contato pelo e-mail informado.
      </div>
      <div
        v-if="submitStatus === 'error'"
        class="p-4 rounded-xl bg-red-50 border border-red-200 text-red-800 text-sm"
      >
        Ocorreu um erro ao enviar o formulário. Tente novamente.
      </div>
    </form>
  </div>
</template>

<script setup lang="ts">
import { reactive, ref } from 'vue'

interface FormData {
  email: string
  nome: string
  responsavel: string
  celular: string
  url: string
  tipo: 'private' | 'public'
  repositorio: string
  metadados: string
}

interface FormErrors {
  email?: string
  nome?: string
  responsavel?: string
  celular?: string
  url?: string
  repositorio?: string
  metadados?: string
}

const form = reactive<FormData>({
  email: '',
  nome: '',
  responsavel: '',
  celular: '',
  url: '',
  tipo: 'private',
  repositorio: '',
  metadados: '',
})

const errors = reactive<FormErrors>({})
const touched = reactive<Record<string, boolean>>({})
const isSubmitting = ref(false)
const submitStatus = ref<'idle' | 'success' | 'error'>('idle')

function touch(field: string) {
  touched[field] = true
  validateField(field)
}

function validateField(field: string) {
  switch (field) {
    case 'email':
      if (!form.email) {
        errors.email = 'E-mail é obrigatório.'
      } else if (!/^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(form.email)) {
        errors.email = 'Informe um e-mail válido.'
      } else {
        delete errors.email
      }
      break

    case 'nome':
      if (!form.nome.trim()) {
        errors.nome = 'Nome do aplicativo é obrigatório.'
      } else {
        delete errors.nome
      }
      break

    case 'responsavel':
      if (!form.responsavel.trim()) {
        errors.responsavel = 'Responsável é obrigatório.'
      } else {
        delete errors.responsavel
      }
      break

    case 'celular': {
      const digits = form.celular.replace(/\D/g, '')
      if (!digits) {
        errors.celular = 'Celular é obrigatório.'
      } else if (digits.length < 10 || digits.length > 11) {
        errors.celular = 'Informe um celular válido com DDD.'
      } else {
        delete errors.celular
      }
      break
    }

    case 'url':
      if (!form.url) {
        errors.url = 'URL do site é obrigatória.'
      } else if (!isValidUrl(form.url)) {
        errors.url = 'Informe uma URL válida (ex: https://meusite.com.br).'
      } else {
        delete errors.url
      }
      break

    case 'repositorio':
      if (form.repositorio && !isValidUrl(form.repositorio)) {
        errors.repositorio = 'Informe uma URL válida para o repositório.'
      } else {
        delete errors.repositorio
      }
      break

    case 'metadados':
      if (form.metadados.trim()) {
        try {
          JSON.parse(form.metadados)
          delete errors.metadados
        } catch {
          errors.metadados = 'Os metadados devem estar em formato JSON válido.'
        }
      } else {
        delete errors.metadados
      }
      break
  }
}

function isValidUrl(value: string): boolean {
  try {
    const url = new URL(value)
    return url.protocol === 'http:' || url.protocol === 'https:'
  } catch {
    return false
  }
}

function maskPhone(event: Event) {
  const input = event.target as HTMLInputElement
  let digits = input.value.replace(/\D/g, '').slice(0, 11)
  if (digits.length <= 10) {
    digits = digits.replace(/^(\d{2})(\d{4})(\d{0,4})/, '($1) $2-$3')
  } else {
    digits = digits.replace(/^(\d{2})(\d{5})(\d{0,4})/, '($1) $2-$3')
  }
  form.celular = digits.replace(/-$/, '')
}

function validateAll(): boolean {
  const fieldsToValidate = ['email', 'nome', 'responsavel', 'celular', 'url', 'repositorio', 'metadados']
  fieldsToValidate.forEach((f) => {
    touched[f] = true
    validateField(f)
  })
  return Object.keys(errors).length === 0
}

async function handleSubmit() {
  if (!validateAll()) return

  isSubmitting.value = true
  submitStatus.value = 'idle'

  try {
    const payload = {
      email: form.email,
      nome: form.nome,
      responsavel: form.responsavel,
      celular: form.celular,
      url: form.url,
      tipo: form.tipo,
      repositorio: form.repositorio || null,
      metadados: form.metadados.trim() ? JSON.parse(form.metadados) : null,
    }

    console.log('Payload:', payload)
    await new Promise((resolve) => setTimeout(resolve, 1000))

    submitStatus.value = 'success'
  } catch {
    submitStatus.value = 'error'
  } finally {
    isSubmitting.value = false
  }
}

function inputClass(field: keyof FormErrors): string {
  const base =
    'w-full px-4 py-2.5 rounded-lg border text-gray-900 placeholder-gray-400 focus:outline-none focus:ring-2 transition-colors'
  if (errors[field] && touched[field]) {
    return `${base} border-red-400 focus:ring-red-300 bg-red-50`
  }
  return `${base} border-gray-300 focus:ring-blue-300 focus:border-blue-400 bg-white`
}

function textareaClass(): string {
  const base =
    'w-full px-4 py-2.5 rounded-lg border text-gray-900 placeholder-gray-400 focus:outline-none focus:ring-2 transition-colors resize-y'
  if (errors.metadados && touched.metadados) {
    return `${base} border-red-400 focus:ring-red-300 bg-red-50`
  }
  return `${base} border-gray-300 focus:ring-blue-300 focus:border-blue-400 bg-white`
}

function typeCardClass(value: string): string {
  if (form.tipo === value) {
    return 'border-blue-500 bg-blue-50 text-blue-700'
  }
  return 'border-gray-200 bg-white text-gray-600 hover:border-gray-300 hover:bg-gray-50'
}
</script>
