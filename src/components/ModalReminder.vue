<template>
  <div v-if="isOpen" class="fixed inset-0 bg-black bg-opacity-50 flex justify-center items-center z-50">
    <div class="bg-teal-800 text-white p-8 rounded-lg w-11/12 sm:w-1/3">
      <h2 class="text-2xl font-bold mb-4 text-center">Defina seu lembrete</h2>

      <form @submit.prevent="submitForm">
        <!-- Título do lembrete -->
        <div class="mb-4">
          <label for="reminderTitle" class="block text-sm font-medium">Nome do remédio</label>
          <input 
            type="text" 
            id="reminderTitle" 
            v-model="form.reminderTitle"
            placeholder="Exemplo: Ibuprofeno"
            class="mt-1 p-2 w-full rounded-md border border-gray-300 focus:ring-2 focus:ring-teal-500 focus:outline-none text-black" 
            required
          />
        </div>

        <!-- Campo da Hora -->
        <div class="mb-4">
          <label for="reminderTime" class="block text-sm font-medium">Hora</label>
          <input 
            type="time" 
            id="reminderTime" 
            v-model="form.reminderTime"
            class="mt-1 p-2 w-full rounded-md border border-gray-300 focus:ring-2 focus:ring-teal-500 focus:outline-none text-black" 
            required
          />
        </div>

        <!-- Campo de Descrição -->
        <div class="mb-4">
          <label for="reminderDescription" class="block text-sm font-medium">Descrição</label>
          <textarea 
            id="reminderDescription" 
            v-model="form.reminderDescription"
            placeholder="Caractísticas, Quantidades, Após refeições."
            class="mt-1 p-2 w-full rounded-md border border-gray-300 focus:ring-2 focus:ring-teal-500 focus:outline-none text-black" 
            rows="4"
          ></textarea>
        </div>

        <!-- Todos os botões -->
        <div class="flex justify-between">
          <button 
            type="submit" 
            class="bg-teal-600 hover:bg-teal-700 text-white py-2 px-4 rounded-lg"
          >
            Salvar Lembrete
          </button>
          <button 
            type="button" 
            @click="closeModal" 
            class="bg-red-600 hover:bg-red-700 text-white py-2 px-4 rounded-lg"
          >
            Fechar
          </button>
        </div>
      </form>
    </div>
  </div>

  <!-- tabela  -->
  <div class="mt-10">
    <table class="min-w-full table-auto bg-teal-800 text-white rounded-lg">
      <thead>
        <tr>
          <th class="px-4 py-2 text-left">Remédio</th>
          <th class="px-4 py-2 text-left">Hora</th>
          <th class="px-4 py-2 text-left">Descrição</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="(reminder, index) in reminders" :key="index">
          <td class="px-4 py-2">{{ reminder.title }}</td>
          <td class="px-4 py-2">{{ reminder.time }}</td>
          <td class="px-4 py-2">{{ reminder.description || 'Sem descrição' }}</td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script setup>
import { ref } from 'vue';

// Props
const props = defineProps({
  isOpen: {
    type: Boolean,
    required: true
  },
  closeModal: {
    type: Function,
    required: true
  }
});

// Dados do formulário e lembretes
const form = ref({
  reminderTitle: '',      // Título do lembrete
  reminderTime: '',       // Hora do lembrete
  reminderDescription: '' // Descrição do lembrete
});

const reminders = ref([]); // Lista para armazenar os lembretes

// Método para salvar o lembrete
const submitForm = () => {
  // Adiciona o novo lembrete à lista de lembretes
  reminders.value.push({
    title: form.value.reminderTitle,
    time: form.value.reminderTime,
    description: form.value.reminderDescription || ''
  });

  // Limpa o formulário após salvar
  form.value.reminderTitle = '';
  form.value.reminderTime = '';
  form.value.reminderDescription = '';

  // Fecha o modal após salvar
  props.closeModal();
};
</script>

<style scoped>
table {
  width: 100%;
  border-collapse: collapse;
}

th, td {
  border: 1px solid #ccc;
  text-align: left;
}

th {
  background-color: #1f4e4e;
  font-weight: bold;
}

tr:nth-child(even) {
  background-color: #2d6f6f;
}

tr:hover {
  background-color: #1f4e4e;
}
</style>
