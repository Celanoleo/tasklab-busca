# TaskLab — Busca Combinada

Controle de tarefas feito em **Vue 3** (Composition API) durante a pós em Desenvolvimento Web com Frameworks Modernos (UTFPR).

## Funcionalidades
- Cadastro de tarefas com validação de título vazio
- Concluir, reabrir e remover tarefas
- Contador de tarefas pendentes que se atualiza sozinho
- Busca por título (ignora maiúsculas e minúsculas)
- Filtro por situação: todas, pendentes ou concluídas
- Busca e filtro funcionando juntos
- Botão para limpar os filtros
- Mensagens diferentes para "lista vazia" e "nenhum resultado"

## Ideia central
A lista `tarefas` é o único dado-fonte. O contador e a lista exibida são valores **derivados** (`computed`), então filtrar nunca apaga nada da lista real.

## Tecnologias
Vue 3 · Vite · JavaScript · HTML · CSS

## Como rodar
Requer Node.js 22.18+ ou 24.12+.

```bash
npm ci
npm run dev
```

Abra a URL que o Vite mostrar no terminal (geralmente `http://localhost:5173/`).
