<template>
  <nav class="bg-white border-gray-200 dark:bg-gray-900">
    <div class="max-w-screen-xl flex flex-wrap items-center justify-between mx-auto p-4">
      <!-- Button for toggling menu on small screens -->
      <button @click="toggleMenu" type="button"
        class="inline-flex items-center p-2 w-10 h-10 justify-center text-sm text-gray-500 rounded-lg md:hidden hover:bg-gray-100 focus:outline-none focus:ring-2 focus:ring-gray-200 dark:text-gray-400 dark:hover:bg-gray-700 dark:focus:ring-gray-600">
        <span class="sr-only">Open main menu</span>
        <svg class="w-5 h-5" aria-hidden="true" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 17 14">
          <path stroke="currentColor" stroke-linecap="round" stroke-linejoin="round" stroke-width="2"
            d="M1 1h15M1 7h15M1 13h15" />
        </svg>
      </button>

      <!-- Single Div for the Logo and Buttons -->
      <div class="w-full flex items-center justify-between">
        <!-- Center the Logo -->
        <div class="flex-1 flex justify-center">
          <a href="/" class="flex items-center">
            <img src="../../assets/logo.png" alt="Logo" class="h-16 w-auto">
          </a>
        </div>

        <!-- Buttons aligned to the right -->
        <div :class="{'hidden': !isMenuOpen, 'block': isMenuOpen}" class="w-full md:w-auto md:flex md:items-center md:ml-auto">
          <ul class="font-medium flex flex-col md:flex-row p-4 md:p-0 mt-4 md:mt-0 border border-gray-100 md:border-0 rounded-lg bg-gray-50 md:bg-white dark:bg-gray-800 md:dark:bg-gray-900 dark:border-gray-700">
            <li v-if="!isLoggedIn" class="md:ml-4">
              <button @click="goToSignupPage" class="ButtonPrimary">S'inscrire</button>
            </li>
            <li v-if="!isLoggedIn" class="md:ml-4">
              <button @click="goToSigninPage" class="ButtonPrimary">Se connecter</button>
            </li>
            <li v-if="isLoggedIn" class="md:ml-4">
              <button @click="logout" class="ButtonPrimary bg-red-600">Deconnexion</button>
              <div v-if="isAdmin" class="md:ml-4">
                <button @click="goToAdminHomePage" class="ButtonPrimary bg-red-600">Admin Page</button>
              </div>
              <button @click="goToEventsPage" class="ButtonPrimary md:ml-4">Annonces</button>
            </li>
          </ul>
        </div>
      </div>
    </div>
  </nav>
</template>

<script setup lang="ts">
import { useRouter } from 'vue-router';
import { ref, onMounted } from 'vue';
import { useJwt } from '@vueuse/integrations/useJwt';

const router = useRouter();
const isMenuOpen = ref(false);
const isLoggedIn = ref(false);
const isAdmin = ref(false);

const toggleMenu = () => {
  isMenuOpen.value = !isMenuOpen.value;
};

const goToSigninPage = () => {
  router.push({ path: '/signin' });
};

const goToSignupPage = () => {
  router.push({ path: '/signup' });
};

const goToAdminHomePage = () => {
  router.push({ path: '/admin' });
};

const goToEventsPage = () => {
  router.push({ path: '/event' });
};

const logout = () => {
  localStorage.removeItem('authToken');
  isLoggedIn.value = false;
  window.location.reload();
};

onMounted(() => {
  isLoggedIn.value = localStorage.getItem('authToken') !== null;
});

onMounted(async () => {
  try {
    const authToken = localStorage.getItem('authToken');
    const { payload } = useJwt(authToken);
    const roleId = payload.value.role;

    if (roleId === 'admin') {
      isAdmin.value = true;
    }
  } catch (error) {
    console.error('Erreur lors de la requête :', error);
  }
});
</script>

<style scoped>
@tailwind base;
@tailwind components;
@tailwind utilities;

.ButtonPrimary {
  @apply w-full md:w-40 h-10 px-6 py-2.5 bg-violet-600 rounded-lg flex justify-center items-center text-white text-base font-bold font-['Inter'] m-2;
}
</style>
