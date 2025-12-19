<script setup>
import { ref, computed } from 'vue'

const metadata = ref({
  materia: '',
  profesor: '',
  carrera: '',
  periodo: '',
  grupo: '',
  fecha: new Date().toLocaleDateString(),
  notas: '',
})

const evaluations = ref([
  { id: 1, percentage: 25, description: '' },
  { id: 2, percentage: 30, description: '' },
])

const projects = ref([
  { id: 1, percentage: 10, description: 'Asistencia y Participación' },
  { id: 2, percentage: 35, description: 'Elaboración de proyecto asignado por profesor' },
])

const addRow = (list) => list.push({ id: Date.now(), percentage: 0, description: '' })
const removeRow = (list, index) => list.splice(index, 1)

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
      class="no-print flex justify-between items-center w-full max-w-4xl mb-4 bg-white p-4 rounded shadow-sm"
    >
      <div class="flex items-center gap-4">
        <span :class="grandTotal === 100 ? 'text-green-600' : 'text-red-600'" class="font-bold">
          Total: {{ grandTotal }}%
        </span>
        <span v-if="grandTotal !== 100" class="text-xs text-red-500 italic">
          (Debe sumar 100%)
        </span>
      </div>
      <button
        @click="printForm"
        class="bg-blue-600 text-white px-6 py-2 rounded hover:bg-blue-700 transition font-medium"
      >
        Imprimir / Guardar PDF
      </button>
    </div>

    <div
      id="printable-area"
      class="bg-white shadow-2xl w-[210mm] min-h-[297mm] p-[15mm] text-sm text-gray-800"
    >
      <div class="flex justify-between items-center mb-8">
        <img src="/uanl_logo.png" alt="UANL" class="w-28 h-auto object-contain" />
        <div class="text-center font-bold text-lg uppercase">Forma de Evaluación</div>
        <img src="/fcfm_logo.webp" alt="FCFM" class="w-32 h-auto object-contain" />
      </div>

      <div class="space-y-6 mb-8 text-center">
        <div class="relative mx-auto w-3/4">
          <input
            v-model="metadata.materia"
            placeholder="Escriba la materia..."
            class="w-full border-b border-black pb-1 text-center outline-none uppercase font-semibold"
          />
          <div class="text-[10px] uppercase mt-1">(Unidad de Aprendizaje)</div>
        </div>

        <div class="relative mx-auto w-3/4">
          <input
            v-model="metadata.profesor"
            placeholder="Escriba el nombre del profesor..."
            class="w-full border-b border-black pb-1 text-center outline-none"
          />
          <div class="text-[10px] uppercase mt-1">(Nombre del Profesor)</div>
        </div>

        <div class="grid grid-cols-4 gap-4 text-center mt-4">
          <div>
            <input
              v-model="metadata.carrera"
              class="w-full border-b border-black pb-1 text-center outline-none"
              placeholder="..."
            />
            <div class="text-[10px] mt-1">(Carrera)</div>
          </div>
          <div>
            <input
              v-model="metadata.periodo"
              class="w-full border-b border-black pb-1 text-center outline-none"
              placeholder="..."
            />
            <div class="text-[10px] mt-1">(Periodo)</div>
          </div>
          <div>
            <input
              v-model="metadata.grupo"
              class="w-full border-b border-black pb-1 text-center outline-none"
              placeholder="..."
            />
            <div class="text-[10px] mt-1">(Grupo)</div>
          </div>
          <div>
            <input
              v-model="metadata.fecha"
              class="w-full border-b border-black pb-1 text-center outline-none"
            />
            <div class="text-[10px] mt-1">(Fecha)</div>
          </div>
        </div>
      </div>

      <div class="mb-6">
        <div class="font-bold mb-2">
          No. de Evaluaciones en el semestre: {{ evaluations.length }}
        </div>
        <table class="w-full border-collapse border border-black">
          <thead class="bg-gray-100 uppercase text-[11px]">
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
                <input v-model="item.description" class="w-full p-1 outline-none px-2" />
              </td>
              <td class="border border-black p-1 no-print text-center">
                <button @click="removeRow(evaluations, index)" class="text-red-500 font-bold">
                  ×
                </button>
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
        <button
          @click="addRow(evaluations)"
          class="no-print mt-2 text-blue-600 text-xs font-semibold hover:underline"
        >
          + Agregar Evaluación
        </button>
      </div>

      <div class="mb-6">
        <div class="font-bold mb-2">No. de Proyectos: {{ projects.length }}</div>
        <table class="w-full border-collapse border border-black">
          <thead class="bg-gray-100 uppercase text-[11px]">
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
                <input v-model="item.description" class="w-full p-1 outline-none px-2" />
              </td>
              <td class="border border-black p-1 no-print text-center">
                <button @click="removeRow(projects, index)" class="text-red-500 font-bold">
                  ×
                </button>
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
        <button
          @click="addRow(projects)"
          class="no-print mt-2 text-blue-600 text-xs font-semibold hover:underline"
        >
          + Agregar Proyecto
        </button>
      </div>

      <div class="mt-8">
        <div class="font-bold text-xs uppercase mb-1">Notas:</div>
        <div class="border border-black w-full min-h-[60px] p-2">
          <textarea
            v-model="metadata.notas"
            placeholder="Observaciones adicionales..."
            class="w-full border-none outline-none resize-none overflow-hidden text-xs italic"
            rows="3"
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
    border: none !important;
  }
}

input::placeholder {
  color: #d1d5db; /* gray-300 */
  font-weight: normal;
  font-style: italic;
  font-size: 0.8rem;
}

input {
  background: transparent;
}
</style>
