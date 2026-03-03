# Register Emergency Hub API

Interface web para cadastro e listagem de aplicativos integrados à **API do Hub de Emergências** — plataforma de emergências da Zona da Mata.

## Tecnologias

- [Vue 3](https://vuejs.org/) + Composition API (`<script setup>`)
- [TypeScript](https://www.typescriptlang.org/)
- [Vite](https://vitejs.dev/)
- [Tailwind CSS v4](https://tailwindcss.com/)
- [Vue Router v5](https://router.vuejs.org/)

## Rotas

| Rota | Descrição |
|------|-----------|
| `/` | Formulário de cadastro de novo aplicativo |
| `/apps` | Listagem dos aplicativos cadastrados |

## Campos do formulário

| Campo | Tipo | Obrigatório |
|-------|------|-------------|
| Nome do Aplicativo | texto | sim |
| E-mail | e-mail | sim |
| Celular | telefone | sim |
| Responsável | texto | sim |
| URL do site | URL | sim |
| Tipo | `private` / `public` | sim |
| Repositório | URL (git) | não |
| Metadados | JSON livre | não |

## Configuração

Crie um arquivo `.env` na raiz do projeto:

```env
# URL base da API (sem barra no final)
VITE_API_URL=
```

Quando `VITE_API_URL` não estiver definido, a listagem exibe dados mockados localmente.

## Desenvolvimento

```bash
npm install
npm run dev
```

## Build

```bash
npm run build
```

## Estrutura

```
src/
├── components/
│   ├── RegistrationForm.vue  # Formulário de cadastro
│   └── AppsList.vue          # Lista de aplicativos
├── views/
│   ├── HomeView.vue          # Rota /
│   └── AppsView.vue          # Rota /apps
├── router/
│   └── index.ts
├── App.vue
└── main.ts
```
