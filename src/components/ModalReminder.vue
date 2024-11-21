<template>
  <div v-if="props.isOpen" class="fixed inset-0 bg-black bg-opacity-50 flex justify-center items-center z-50">
    <div class="bg-teal-800 text-white p-8 rounded-lg w-11/12 sm:w-1/3">
      <h2 class="text-2xl font-bold mb-4 text-center">{{ editingIndex !== null ? 'Editar Lembrete' : 'Defina seu lembrete' }}</h2>

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

        <!-- Botões -->
        <div class="flex justify-between">
          <button 
            type="submit" 
            class="bg-teal-600 hover:bg-teal-700 text-white py-2 px-4 rounded-lg"
          >
            {{ editingIndex !== null ? 'Salvar Alterações' : 'Salvar Lembrete' }}
          </button>
          <button 
            type="button" 
            @click="props.closeModal" 
            class="bg-red-600 hover:bg-red-700 text-white py-2 px-4 rounded-lg"
          >
            Fechar
          </button>
        </div>
      </form>
    </div>
  </div>

  <!-- Tabela -->
  <div class="mt-10">
    <table class="min-w-full table-auto bg-teal-800 text-white rounded-lg">
      <thead>
        <tr>
          <th class="px-4 py-2 text-left">Remédio</th>
          <th class="px-4 py-2 text-left">Hora</th>
          <th class="px-4 py-2 text-left">Descrição</th>
          <th class="px-4 py-2 text-left">Ações</th> <!-- Nova coluna de Ações -->
        </tr>
      </thead>
      <tbody>
        <tr v-for="(reminder, index) in reminders" :key="index">
          <td class="px-4 py-2">{{ reminder.title }}</td>
          <td class="px-4 py-2">{{ reminder.time }}</td>
          <td class="px-4 py-2">{{ reminder.description || 'Sem descrição' }}</td>
          <td class="px-4 py-2 flex justify-start space-x-2">
            <!-- Botões de Editar e Excluir -->
            <button 
              @click="editReminder(index)" 
              class="bg-yellow-500 hover:bg-yellow-600 text-white py-1 px-3 rounded-lg"
            >
              Editar
            </button>
            <button 
              @click="deleteReminder(index)" 
              class="bg-red-600 hover:bg-red-700 text-white py-1 px-3 rounded-lg"
            >
              Excluir
            </button>
          </td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue';

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
const editingIndex = ref(null); // Índice do lembrete que está sendo editado

// Carregar os lembretes do localStorage quando a página for montada
onMounted(() => {
  const savedReminders = localStorage.getItem('reminders');
  if (savedReminders) {
    reminders.value = JSON.parse(savedReminders);
  }

  // Solicitar permissão para notificações
  if (Notification.permission !== "granted") {
    Notification.requestPermission();
  }
});

// Função para salvar ou editar o lembrete
const submitForm = () => {
  if (form.value.reminderTitle && form.value.reminderTime) {
    const newReminder = {
      title: form.value.reminderTitle,
      time: form.value.reminderTime,
      description: form.value.reminderDescription || ''
    };

    if (editingIndex.value !== null) {
      // Atualiza o lembrete existente
      reminders.value[editingIndex.value] = newReminder;
    } else {
      // Adiciona um novo lembrete
      reminders.value.push(newReminder);
    }

    // Salva os lembretes no localStorage
    localStorage.setItem('reminders', JSON.stringify(reminders.value));

    // Define o alarme
    setAlarm(newReminder);

    // Limpa o formulário após salvar
    form.value.reminderTitle = '';
    form.value.reminderTime = '';
    form.value.reminderDescription = '';

    // Fecha o modal após salvar
    props.closeModal();

    // Reseta o índice de edição
    editingIndex.value = null;
  }
};

// Função para definir o alarme
const setAlarm = (reminder) => {
  const now = new Date();
  const [hour, minute] = reminder.time.split(':').map(Number);

  const alarmTime = new Date(now);
  alarmTime.setHours(hour, minute, 0, 0);

  // Se a hora do alarme já passou hoje, marca para o próximo dia
  if (alarmTime < now) {
    alarmTime.setDate(now.getDate() + 1);
  }

  const timeDifference = alarmTime - now;

  setTimeout(() => {
    triggerAlarm(reminder);
  }, timeDifference);
};

// Função para disparar o alarme (notificação)
const triggerAlarm = (reminder) => {
  if (Notification.permission === "granted") {
    new Notification(`Hora de tomar ${reminder.title}`, {
      body: reminder.description || 'Não há descrição.',
      icon: '/icon.png',
    });
  }
};

// Função para editar um lembrete
const editReminder = (index) => {
  const reminderToEdit = reminders.value[index];
  form.value.reminderTitle = reminderToEdit.title;
  form.value.reminderTime = reminderToEdit.time;
  form.value.reminderDescription = reminderToEdit.description;

  // Define o índice de edição
  editingIndex.value = index;

  // Abre o modal para edição (não precisa fechar o modal, pois ele deve abrir)
  props.isOpen = true;
};

// Função para excluir um lembrete
const deleteReminder = (index) => {
  reminders.value.splice(index, 1);

  // Atualiza o localStorage após excluir
  localStorage.setItem('reminders', JSON.stringify(reminders.value));
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
  font-weight:
 bold;
}

tr:nth-child(even) {
  background-color: #2d6f6f;
}

tr:hover {
  background-color: #1f4e4e;
}
</style>
