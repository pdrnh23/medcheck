<template>
  <div>
    <div class="bg-teal-900 w-full h-16 flex items-center justify-center">
      <h1 class="mb-2 text-2xl font-extrabold text-gray-900 dark:text-white md:text-3xl lg:text-4xl text-center">
        <span class="text-transparent bg-clip-text bg-gradient-to-r to-emerald-600 from-sky-400">Med</span>Check
      </h1>
    </div>

    <div class="bg-teal-800 w-full py-8 flex flex-col items-center justify-center text-center">
      <h1 class="text-white text-lg sm:text-xl md:text-2xl font-medium mb-4">Lembre-se de cuidar de você</h1>
      
      <div class="flex justify-center gap-6 mb-4">
        <img class="max-w-full h-auto w-1/6 sm:w-1/5 md:w-1/6 lg:w-1/6" src="./assets/img/relogio.png" alt="Relógio">
        <img class="max-w-full h-auto w-1/6 sm:w-1/6 md:w-1/6 lg:w-1/6 translate-y-2" src="./assets/img/pilulas.png" alt="Pílulas">
      </div>

      <h1 class="text-white text-lg sm:text-xl md:text-2xl font-medium mt-4">No seu tempo</h1>
    </div>

    <div class="flex flex-col items-center mt-6 mb-8">
      <button 
        type="button" 
        class="text-white bg-[#24292F] hover:bg-[#24292F]/90 focus:ring-4 focus:outline-none focus:ring-[#24292F]/50 font-medium rounded-lg text-sm px-6 py-3 text-center inline-flex items-center gap-3 transition duration-300 ease-in-out transform hover:scale-105 dark:focus:ring-gray-500 dark:hover:bg-[#050708]/30"
        @click="openModal = true"
      >
        Definir lembrete
        <img class="max-w-full h-auto w-5 sm:w-6 md:w-6 lg:w-7" src="./assets/img/caneta.png" alt="Caneta">
      </button>
    </div>

    <!-- Modal com o formulário -->
    <ModalReminder 
      :isOpen="openModal" 
      :closeModal="closeModal" 
    />
  </div>
</template>

<script setup>
import { ref } from 'vue';
import ModalReminder from './components/ModalReminder.vue'; // Importando o componente ModalReminder

// Estado para controlar se o modal está aberto ou fechado
const openModal = ref(false);

// Função para fechar o modal
const closeModal = () => {
  openModal.value = false;
};
</script>

<style scoped>
/* Estilos do componente App.vue */
</style>
