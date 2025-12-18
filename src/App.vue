<script setup>
import { ref, computed } from 'vue'

// State for the form metadata
const metadata = ref({
  materia: '',
  profesor: '',
  carrera: '',
  periodo: '',
  grupo: '',
  fecha: new Date().toLocaleDateString(),
  notas: '',
})

// State for tables
const evaluations = ref([
  { id: 1, percentage: 25, description: '' },
  { id: 2, percentage: 30, description: '' },
])

const projects = ref([
  { id: 1, percentage: 10, description: 'Asistencia y Participación' },
  { id: 2, percentage: 35, description: 'Elaboración de proyecto asignado por profesor' },
])

// Helper to add/remove rows
const addRow = (list) => list.push({ id: Date.now(), percentage: 0, description: '' })
const removeRow = (list, index) => list.splice(index, 1)

// Totals calculation
const evalTotal = computed(() =>
  evaluations.value.reduce((sum, item) => sum + Number(item.percentage), 0),
)
const projectTotal = computed(() =>
  projects.value.reduce((sum, item) => sum + Number(item.percentage), 0),
)
const grandTotal = computed(() => evalTotal.value + projectTotal.value)

const printForm = () => {
  window.print()
}
</script>

<template>
  <div class="min-h-screen bg-gray-100 p-8 flex flex-col items-center">
    <div
      class="no-print bg-white p-6 rounded-lg shadow-md mb-8 w-full max-w-4xl border-l-4 border-blue-600"
    >
      <h2 class="text-xl font-bold mb-4">Generador de Forma de Evaluación</h2>
      <div class="grid grid-cols-2 gap-4 mb-6">
        <input
          v-model="metadata.materia"
          placeholder="Unidad de Aprendizaje"
          class="border p-2 rounded"
        />
        <input
          v-model="metadata.profesor"
          placeholder="Nombre del Profesor"
          class="border p-2 rounded"
        />
        <input v-model="metadata.carrera" placeholder="Carrera" class="border p-2 rounded" />
        <input
          v-model="metadata.periodo"
          placeholder="Periodo Semestral"
          class="border p-2 rounded"
        />
      </div>

      <div class="flex justify-between items-center">
        <p :class="grandTotal === 100 ? 'text-green-600' : 'text-red-600'" class="font-bold">
          Total General: {{ grandTotal }}% (Debe ser 100%)
        </p>
        <button
          @click="printForm"
          class="bg-blue-600 text-white px-6 py-2 rounded hover:bg-blue-700 transition"
        >
          Imprimir / Guardar PDF
        </button>
      </div>
    </div>

    <div
      id="printable-area"
      class="bg-white shadow-2xl w-[210mm] min-h-[297mm] p-[15mm] text-sm text-gray-800"
    >
      <div class="flex justify-between items-center mb-8">
        <div class="flex items-center gap-2">
          <img src="/uanl_logo.png" alt="UANL Logo" class="w-28 h-auto object-contain" />
        </div>
        <div class="text-center font-bold text-lg uppercase">Forma de Evaluación</div>
        <div class="flex items-center gap-2 text-right">
          <img src="/fcfm_logo.webp" alt="FCFM Logo" class="w-32 h-auto object-contain" />
        </div>
      </div>

      <div class="space-y-6 mb-8 text-center">
        <div class="border-b border-black mx-auto w-3/4 pb-1">{{ metadata.materia || ' ' }}</div>
        <div class="text-[10px] uppercase mt-[-20px]">(Unidad de Aprendizaje)</div>

        <div class="border-b border-black mx-auto w-3/4 pb-1">{{ metadata.profesor || ' ' }}</div>
        <div class="text-[10px] uppercase mt-[-20px]">(Nombre del Profesor)</div>

        <div class="grid grid-cols-4 gap-4 text-center mt-4">
          <div>
            <div class="border-b border-black pb-1">{{ metadata.carrera }}</div>
            <div class="text-[10px] mt-1">(Carrera)</div>
          </div>
          <div>
            <div class="border-b border-black pb-1">{{ metadata.periodo }}</div>
            <div class="text-[10px] mt-1">(Periodo)</div>
          </div>
          <div>
            <div class="border-b border-black pb-1">{{ metadata.grupo }}</div>
            <div class="text-[10px] mt-1">(Grupo)</div>
          </div>
          <div>
            <div class="border-b border-black pb-1">{{ metadata.fecha }}</div>
            <div class="text-[10px] mt-1">(Fecha)</div>
          </div>
        </div>
      </div>

      <div class="mb-6">
        <div class="font-bold mb-2">
          No. de Evaluaciones en el semestre: {{ evaluations.length }}
        </div>
        <table class="w-full border-collapse border border-black">
          <thead class="bg-gray-200">
            <tr>
              <th class="border border-black p-1 w-24">Evaluación</th>
              <th class="border border-black p-1 w-16">%</th>
              <th class="border border-black p-1">Descripción</th>
              <th class="border border-black p-1 no-print w-10"></th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="(item, index) in evaluations" :key="item.id">
              <td class="border border-black p-1 text-center">{{ index + 1 }}</td>
              <td class="border border-black p-0">
                <input
                  type="number"
                  v-model="item.percentage"
                  class="w-full p-1 text-center outline-none"
                />
              </td>
              <td class="border border-black p-0">
                <input v-model="item.description" class="w-full p-1 outline-none" />
              </td>
              <td class="border border-black p-1 no-print text-center">
                <button @click="removeRow(evaluations, index)" class="text-red-500">×</button>
              </td>
            </tr>
            <tr class="font-bold bg-gray-50">
              <td class="border border-black p-1 text-right">Total</td>
              <td class="border border-black p-1 text-center">{{ evalTotal }}</td>
              <td class="border border-black p-1"></td>
              <td class="no-print border border-black"></td>
            </tr>
          </tbody>
        </table>
        <button @click="addRow(evaluations)" class="no-print mt-2 text-blue-500 text-xs">
          + Agregar Evaluación
        </button>
      </div>

      <div class="mb-6">
        <div class="font-bold mb-2">No. de Proyectos: {{ projects.length }}</div>
        <table class="w-full border-collapse border border-black">
          <thead class="bg-gray-200">
            <tr>
              <th class="border border-black p-1 w-24">Proyectos</th>
              <th class="border border-black p-1 w-16">%</th>
              <th class="border border-black p-1">Descripción</th>
              <th class="border border-black p-1 no-print w-10"></th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="(item, index) in projects" :key="item.id">
              <td class="border border-black p-1 text-center">{{ index + 1 }}</td>
              <td class="border border-black p-0">
                <input
                  type="number"
                  v-model="item.percentage"
                  class="w-full p-1 text-center outline-none"
                />
              </td>
              <td class="border border-black p-0">
                <input v-model="item.description" class="w-full p-1 outline-none" />
              </td>
              <td class="border border-black p-1 no-print text-center">
                <button @click="removeRow(projects, index)" class="text-red-500">×</button>
              </td>
            </tr>
            <tr class="font-bold bg-gray-50">
              <td class="border border-black p-1 text-right">Total</td>
              <td class="border border-black p-1 text-center">{{ projectTotal }}</td>
              <td class="border border-black p-1"></td>
              <td class="no-print border border-black"></td>
            </tr>
          </tbody>
        </table>
        <button @click="addRow(projects)" class="no-print mt-2 text-blue-500 text-xs">
          + Agregar Proyecto
        </button>
      </div>

      <div class="mt-8">
        <div class="font-bold">Notas</div>
        <div class="border border-black w-full min-h-[40px] p-2 mt-1">
          <textarea
            v-model="metadata.notas"
            class="w-full border-none outline-none resize-none overflow-hidden"
            rows="2"
          ></textarea>
        </div>
      </div>

      <div class="mt-12 flex justify-between text-[10px] border-t pt-2 border-gray-300">
        <div>Revisión: 002</div>
        <div>Hoja 1 de 1</div>
        <div>FO-SAC-CDC/002</div>
      </div>
    </div>
  </div>
</template>

<style scoped>
@media print {
  .no-print {
    display: none !important;
  }
  body,
  .min-h-screen {
    background: white !important;
    padding: 0 !important;
  }
  #printable-area {
    box-shadow: none !important;
    margin: 0 !important;
    width: 100% !important;
  }
}

input {
  background: transparent;
}
</style>
