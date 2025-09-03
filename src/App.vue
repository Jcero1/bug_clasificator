<script setup>
import { computed, ref } from 'vue'

const opciones = [
  { id: 1, opcion: 'categorias' },
  { id: 2, opcion: 'bugs' },
  { id: 3, opcion: 'computadoras' },
]
const selectedOption = ref(3)

const data = {
  computadoras: [
    {
      id: 1,
      categoria: 'C',
      bugs: ['bug 2', 'bug 5', 'bug 7'],
    },
    {
      id: 2,
      categoria: 'A',
      bugs: ['bug 1', 'bug 3', 'bug 4', 'bug 6'],
    },
    {
      id: 3,
      categoria: 'D',
      bugs: ['bug 2', 'bug 3', 'bug 5', 'bug 7', 'bug 1'],
    },
    {
      id: 4,
      categoria: 'B',
      bugs: ['bug 4', 'bug 6', 'bug 2'],
    },
    {
      id: 5,
      categoria: 'A',
      bugs: ['bug 1', 'bug 5', 'bug 7', 'bug 3', 'bug 2'],
    },
    {
      id: 6,
      categoria: 'C',
      bugs: ['bug 6', 'bug 1', 'bug 4', 'bug 7'],
    },
    {
      id: 7,
      categoria: 'D',
      bugs: ['bug 3', 'bug 5', 'bug 2', 'bug 6', 'bug 1'],
    },
    {
      id: 8,
      categoria: 'B',
      bugs: ['bug 7', 'bug 4', 'bug 2'],
    },
    {
      id: 9,
      categoria: 'A',
      bugs: ['bug 1', 'bug 2', 'bug 5', 'bug 6', 'bug 3', 'bug 7'],
    },
    {
      id: 10,
      categoria: 'C',
      bugs: ['bug 4', 'bug 6', 'bug 1', 'bug 5'],
    },
  ],
}

const groupByCategory = () => {
  return Object.entries(
    data.computadoras.reduce((acc, comp) => {
      if (!acc[comp.categoria]) acc[comp.categoria] = []
      acc[comp.categoria].push(comp)
      return acc
    }, {}),
  ).map(([categoria, computadoras]) => ({
    categoria,
    computadoras,
  }))
}

const groupedByBug = () => {
  return Object.entries(
    data.computadoras.reduce((acc, comp) => {
      comp.bugs.forEach((bug) => {
        if (!acc[bug]) acc[bug] = []
        acc[bug].push({ id: comp.id, categoria: comp.categoria })
      })
      return acc
    }, {}),
  ).map(([bug, computadoras]) => ({
    bug,
    computadoras,
  }))
}

const organizer = new Map([
  [1, groupByCategory()],
  [2, groupedByBug()],
  [3, data.computadoras],
])

const filtrarData = computed(() => {
  return organizer.get(selectedOption.value)
})

const imprimir = () => {
  console.log(filtrarData.value)
}
</script>

<template>
  <div id="container">
    <div>
      <select name="select" id="" v-model="selectedOption" @change="imprimir">
        <option v-for="opcion in opciones" :key="opcion.id" :value="opcion.id">
          {{ opcion.opcion }}
        </option>
      </select>
    </div>

    <div>
      <!-- tabla para mostrar por computadora -->
      <table v-if="selectedOption === 3">
        <thead>
          <tr>
            <th v-for="(nombre, index) in ['computadora', 'categoria', 'bug']" :key="index">
              {{ nombre }}
            </th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="computadora in filtrarData" :key="computadora.id">
            <td>{{ computadora.id }}</td>
            <td>{{ computadora.categoria }}</td>
            <td>
              <span v-for="bug in computadora.bugs" :key="bug">{{ bug + ', ' }}</span>
            </td>
          </tr>
        </tbody>
      </table>

      <!-- tabla para mostrar por categoria -->
      <table v-if="selectedOption === 1">
        <thead>
          <tr>
            <th v-for="(nombre, index) in ['categoria', 'computadora', 'bug']" :key="index">
              {{ nombre }}
            </th>
          </tr>
        </thead>
        <tbody>
          <template v-for="(categoria, index) in filtrarData" :key="index">
            <tr v-for="(computadora, idx) in categoria.computadoras" :key="computadora.id">
              <!-- Solo en la primera computadora de la categoría se muestra el td con rowspan -->
              <td v-if="idx === 0" :rowspan="categoria.computadoras.length">
                {{ categoria.categoria }}
              </td>
              <td>{{ computadora.id }}</td>
              <td>
                <span v-for="bug in computadora.bugs" :key="bug">{{ bug + ', ' }}</span>
              </td>
            </tr>
          </template>
        </tbody>
      </table>

      <!-- tabla para mostrar por bugs -->
      <table v-if="selectedOption === 2">
        <thead>
          <tr>
            <th v-for="(nombre, index) in ['bug', 'computadora', 'categoria']" :key="index">
              {{ nombre }}
            </th>
          </tr>
        </thead>
        <tbody>
          <template v-for="(bug, index) in filtrarData" :key="index">
            <tr v-for="(computadora, idx) in bug.computadoras" :key="computadora.id">
              <!-- Solo en la primera computadora de la categoría se muestra el td con rowspan -->
              <td v-if="idx === 0" :rowspan="bug.computadoras.length">
                {{ bug.bug }}
              </td>
              <td>{{ computadora.id }}</td>
              <td>
                {{ computadora.categoria }}
              </td>
            </tr>
          </template>
        </tbody>
      </table>
    </div>
  </div>
</template>

<style scoped>
div {
  margin: 20px;
  display: block;
}
#container {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
}
td,
th {
  border: 1px solid white;
  padding: 10px;
}
</style>
