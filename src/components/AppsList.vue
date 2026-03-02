<template>
  <div class="w-full max-w-3xl">
    <div class="flex items-center justify-between mb-8">
      <div>
        <h1 class="text-3xl font-bold text-gray-900">Aplicativos Cadastrados</h1>
        <p class="mt-1 text-gray-500">Lista de aplicativos registrados na Emergency Hub API</p>
      </div>
      <RouterLink
        to="/"
        class="px-4 py-2 rounded-lg text-sm font-medium text-blue-600 border border-blue-200 hover:bg-blue-50 transition-colors"
      >
        + Novo Cadastro
      </RouterLink>
    </div>

    <div v-if="loading" class="flex justify-center py-20">
      <svg class="animate-spin w-8 h-8 text-blue-500" fill="none" viewBox="0 0 24 24">
        <circle class="opacity-25" cx="12" cy="12" r="10" stroke="currentColor" stroke-width="4" />
        <path class="opacity-75" fill="currentColor" d="M4 12a8 8 0 018-8v8H4z" />
      </svg>
    </div>

    <div v-else-if="error" class="p-6 rounded-xl bg-red-50 border border-red-200 text-red-700 text-sm">
      {{ error }}
    </div>

    <div v-else-if="apps.length === 0" class="text-center py-20 text-gray-400">
      Nenhum aplicativo cadastrado ainda.
    </div>

    <div v-else class="bg-white rounded-2xl shadow-sm border border-gray-100 overflow-hidden">
      <div class="hidden sm:grid grid-cols-[2fr_1fr_2fr_1fr] gap-4 px-6 py-3 bg-gray-50 border-b border-gray-100 text-xs font-semibold text-gray-500 uppercase tracking-wide">
        <span>Projeto</span>
        <span>Responsável</span>
        <span>Site</span>
        <span>Cadastro</span>
      </div>

      <div
        v-for="(app, index) in apps"
        :key="app.uuid"
        :class="index !== apps.length - 1 ? 'border-b border-gray-100' : ''"
        class="grid grid-cols-1 sm:grid-cols-[2fr_1fr_2fr_1fr] gap-1 sm:gap-4 px-6 py-4 hover:bg-gray-50 transition-colors"
      >
        <span class="font-medium text-gray-900 truncate">{{ app.nome ?? app.slug }}</span>
        <span class="text-gray-600 truncate">{{ app.responsavel ?? '—' }}</span>
        <a
          :href="app.url"
          target="_blank"
          rel="noopener noreferrer"
          class="text-blue-600 hover:underline truncate"
        >{{ app.url }}</a>
        <span class="text-gray-500 text-sm">{{ formatDate(app.created_at) }}</span>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, onMounted } from 'vue'

interface AppItem {
  uuid: string
  slug: string
  email: string
  nome: string | null
  responsavel: string | null
  repositorio: string | null
  tipo: 'private' | 'public'
  url: string
  metadados: Record<string, unknown> | null
  created_at: string
}

const API_URL = import.meta.env.VITE_API_URL
  ? `${import.meta.env.VITE_API_URL}/apps`
  : null

const MOCK_DATA: AppItem[] = [
  {
    uuid: 'a1b2c3d4-1111-4aaa-8abc-111111111111',
    slug: 'fintech-pagamentos',
    email: 'dev@fintechpay.com.br',
    nome: 'FinTech Pagamentos',
    responsavel: 'Camila Souza',
    repositorio: 'https://github.com/fintechpay/api-core',
    tipo: 'private',
    url: 'https://fintechpay.com.br',
    metadados: { linguagem: 'Go', framework: 'Gin', database: 'PostgreSQL' },
    created_at: '2026-01-08T09:15:00Z',
  },
  {
    uuid: 'b2c3d4e5-2222-4bbb-9bcd-222222222222',
    slug: 'monitor-iot',
    email: 'suporte@iotbrasil.io',
    nome: 'Monitor IoT',
    responsavel: 'Felipe Andrade',
    repositorio: null,
    tipo: 'public',
    url: 'https://iotbrasil.io',
    metadados: { linguagem: 'Python', framework: 'FastAPI', versao: '2.1.0' },
    created_at: '2026-01-22T14:00:00Z',
  },
  {
    uuid: 'c3d4e5f6-3333-4ccc-abcd-333333333333',
    slug: 'saude-conectada',
    email: 'ti@saudeconectada.med.br',
    nome: 'Saúde Conectada',
    responsavel: 'Dra. Ana Peixoto',
    repositorio: 'https://github.com/saudeconectada/backend',
    tipo: 'private',
    url: 'https://saudeconectada.med.br',
    metadados: { linguagem: 'Java', framework: 'Spring Boot', database: 'MySQL' },
    created_at: '2026-02-03T11:30:00Z',
  },
  {
    uuid: 'd4e5f6a7-4444-4ddd-bcde-444444444444',
    slug: 'logistica-express',
    email: 'api@logex.com.br',
    nome: 'Logística Express',
    responsavel: 'Bruno Martins',
    repositorio: 'https://github.com/logex/rastreamento-api',
    tipo: 'private',
    url: 'https://logex.com.br',
    metadados: { linguagem: 'Node.js', framework: 'NestJS', database: 'MongoDB' },
    created_at: '2026-02-10T08:45:00Z',
  },
  {
    uuid: 'e5f6a7b8-5555-4eee-cdef-555555555555',
    slug: 'educatech-lms',
    email: 'contato@educatech.com.br',
    nome: 'EducaTech LMS',
    responsavel: 'Mariana Lima',
    repositorio: null,
    tipo: 'public',
    url: 'https://educatech.com.br',
    metadados: { linguagem: 'Ruby', framework: 'Rails', versao: '1.4.2' },
    created_at: '2026-02-17T16:20:00Z',
  },
  {
    uuid: 'f6a7b8c9-6666-4fff-def0-666666666666',
    slug: 'agro-gestao',
    email: 'dev@agrogestao.agr.br',
    nome: 'AgroGestão',
    responsavel: 'João Carvalho',
    repositorio: 'https://github.com/agrogestao/core-api',
    tipo: 'private',
    url: 'https://agrogestao.agr.br',
    metadados: { linguagem: 'Rust', framework: 'Axum', database: 'PostgreSQL' },
    created_at: '2026-02-24T10:05:00Z',
  },
  {
    uuid: 'a7b8c9d0-7777-4000-ef01-777777777777',
    slug: 'chat-suporte',
    email: 'hello@chatsuporte.app',
    nome: null,
    responsavel: 'Letícia Ferreira',
    repositorio: 'https://github.com/chatsuporte/websocket-server',
    tipo: 'public',
    url: 'https://chatsuporte.app',
    metadados: { linguagem: 'Elixir', framework: 'Phoenix', realtime: true },
    created_at: '2026-02-28T13:50:00Z',
  },
  {
    uuid: 'b8c9d0e1-8888-4111-f012-888888888888',
    slug: 'crm-vendas-pro',
    email: 'suporte@crmpro.com.br',
    nome: 'CRM Vendas Pro',
    responsavel: 'Rafael Mendes',
    repositorio: null,
    tipo: 'private',
    url: 'https://crmpro.com.br',
    metadados: null,
    created_at: '2026-03-01T17:00:00Z',
  },
  {
    uuid: 'c9d0e1f2-9999-4222-0123-999999999999',
    slug: 'open-weather-br',
    email: 'dev@openweatherbr.io',
    nome: 'Open Weather BR',
    responsavel: null,
    repositorio: 'https://github.com/openweatherbr/ingest-api',
    tipo: 'public',
    url: 'https://openweatherbr.io',
    metadados: { linguagem: 'Python', framework: 'Django', database: 'TimescaleDB' },
    created_at: '2026-03-02T07:30:00Z',
  },
]

const apps = ref<AppItem[]>([])
const loading = ref(true)
const error = ref<string | null>(null)

function formatDate(iso: string): string {
  return new Date(iso).toLocaleDateString('pt-BR', {
    day: '2-digit',
    month: '2-digit',
    year: 'numeric',
  })
}

onMounted(async () => {
  if (!API_URL) {
    apps.value = MOCK_DATA
    loading.value = false
    return
  }
  try {
    const res = await fetch(API_URL)
    if (!res.ok) throw new Error(`Erro ${res.status}: ${res.statusText}`)
    const json = await res.json() as { data: AppItem[] }
    apps.value = json.data
  } catch (err) {
    error.value = err instanceof Error ? err.message : 'Erro ao carregar aplicativos.'
  } finally {
    loading.value = false
  }
})
</script>
