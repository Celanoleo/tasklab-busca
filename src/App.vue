<script setup>
import { ref, computed } from 'vue'

// ---------- DADO-FONTE (única lista "real") ----------
const tarefas = ref([
  { id: '1', titulo: 'Ler Vue', concluida: false },
  { id: '2', titulo: 'Praticar CSS', concluida: true },
  { id: '3', titulo: 'Revisar Vue', concluida: true },
])

// ---------- Estado do formulário ----------
const titulo = ref('')
const erro = ref('')

// ---------- Estado dos filtros (só guardam critérios) ----------
const busca = ref('')
const situacao = ref('todas') // 'todas' | 'pendentes' | 'concluidas'

// ---------- Valores DERIVADOS (computed) ----------
// Desafio 1: contador recalculado sozinho sempre que "tarefas" muda
const totalPendentes = computed(() => tarefas.value.filter((t) => !t.concluida).length)

// Desafios 2, 3, 4 e 5: lista para exibição = tarefas + busca + situação
// (não altera "tarefas": filter cria um array novo)
const tarefasVisiveis = computed(() => {
  const termo = busca.value.trim().toLowerCase()
  return tarefas.value.filter((t) => {
    const passaTexto = t.titulo.toLowerCase().includes(termo) // '' inclui tudo
    const passaSituacao =
      situacao.value === 'todas' ||
      (situacao.value === 'pendentes' && !t.concluida) ||
      (situacao.value === 'concluidas' && t.concluida)
    return passaTexto && passaSituacao // texto E situação
  })
})

// ---------- Ações ----------
function enviar() {
  const texto = titulo.value.trim()
  if (!texto) {
    erro.value = 'Informe um título.'
    return
  }
  tarefas.value.push({ id: crypto.randomUUID(), titulo: texto, concluida: false })
  titulo.value = ''
  erro.value = ''
}

function alternar(id) {
  const tarefa = tarefas.value.find((t) => t.id === id)
  if (tarefa) tarefa.concluida = !tarefa.concluida
}

function remover(id) {
  tarefas.value = tarefas.value.filter((t) => t.id !== id)
}

// Desafio 6: mexe só nos filtros, nunca em "tarefas"
function limparFiltros() {
  busca.value = ''
  situacao.value = 'todas'
}
</script>

<template>
<main>
  <h1>TaskLab</h1>
  <p>Cadastro, busca e filtro combinados</p>

  <form @submit.prevent="enviar" novalidate>
    <label for="nova-tarefa">Nova tarefa</label>
    <div class="linha">
      <input id="nova-tarefa" v-model.trim="titulo" aria-describedby="erro-form">
      <button type="submit">Adicionar</button>
    </div>
    <p id="erro-form" class="erro" role="alert">{{ erro }}</p>
  </form>

  <section class="filtros" aria-label="Filtros">
    <div>
      <label for="busca">Buscar por título</label>
      <input id="busca" type="search" v-model="busca" placeholder="Ex.: vue">
    </div>
    <div>
      <label for="situacao">Situação</label>
      <select id="situacao" v-model="situacao">
        <option value="todas">Todas</option>
        <option value="pendentes">Pendentes</option>
        <option value="concluidas">Concluídas</option>
      </select>
    </div>
    <button type="button" class="secundario" @click="limparFiltros">Limpar filtros</button>
  </section>

  <p role="status">{{ totalPendentes }} pendente(s) · {{ tarefasVisiveis.length }} de {{ tarefas.length }} exibida(s)</p>

  <!-- Desafio 7: cada mensagem observa uma lista diferente -->
  <p v-if="tarefas.length === 0">Nenhuma tarefa cadastrada.</p>
  <p v-else-if="tarefasVisiveis.length === 0">Nenhuma tarefa corresponde aos filtros.</p>

  <ul v-else>
    <li v-for="tarefa in tarefasVisiveis" :key="tarefa.id" class="item" :class="{ concluida: tarefa.concluida }">
      <div>
        <strong>{{ tarefa.titulo }}</strong>
        <span class="situacao">{{ tarefa.concluida ? 'Concluída' : 'Pendente' }}</span>
      </div>
      <div class="acoes">
        <button type="button" @click="alternar(tarefa.id)">{{ tarefa.concluida ? 'Reabrir' : 'Concluir' }}</button>
        <button type="button" @click="remover(tarefa.id)">Remover</button>
      </div>
    </li>
  </ul>
</main>
</template>

<style scoped>
main { width: min(720px, 92%); margin: 2rem auto; font-family: system-ui, sans-serif; color: #18342b; }
button, input, select { font: inherit; padding: .6rem; border: 1px solid #768b80; border-radius: .4rem; }
button { cursor: pointer; color: #fff; background: #176b50; }
button.secundario { background: #fff; color: #176b50; }
form, .filtros { background: #fff; border: 1px solid #bdcfc5; border-radius: .6rem; padding: 1rem; margin: 1rem 0; }
.filtros { display: flex; gap: .8rem; flex-wrap: wrap; align-items: flex-end; }
.filtros label { display: block; font-size: .9rem; margin-bottom: .2rem; }
.linha { display: flex; gap: .6rem; flex-wrap: wrap; }
.linha input { flex: 1; min-width: 150px; }
.erro { color: #a12525; min-height: 1.2rem; }
ul { list-style: none; padding: 0; }
.item { background: #fff; padding: 1rem; margin: .7rem 0; border: 1px solid #bdcfc5; border-radius: .5rem; display: flex; flex-wrap: wrap; justify-content: space-between; gap: 1rem; }
.item.concluida strong { text-decoration: line-through; }
.situacao { display: block; font-size: .9rem; margin-top: .3rem; }
.acoes { display: flex; gap: .5rem; align-items: center; }
</style>
