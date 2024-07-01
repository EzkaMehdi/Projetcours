<template>
    <div>
      <HeaderPage />
      <section class="flex flex-col md:flex-row justify-center items-center p-4 md:p-16">
        <div class="text-center md:text-left md:mr-8 flex-1">
          <h1
            class="font-inter text-3xl md:text-5xl font-extrabold leading-tight tracking-normal text-transparent bg-clip-text bg-gradient-to-r from-purple-400 to-pink-600 mb-6">
            Les Meilleurs Evenements sont tout autour de vous !
          </h1>
          <h3 class="font-inter text-2xl md:text-3xl font-extrabold leading-tight tracking-normal mb-6">
            Proposez ou assistez aux meilleurs évenements proche de chez vous ou que vous soyez !
          </h3>
          <div class="flex flex-col md:flex-row justify-center md:justify-start">
            <button
              class="ButtonPrimary md:w-80 h-12 px-6 m-2 py-2.5 bg-violet-600 rounded-lg flex justify-center items-center"
              @click="goToCityPage">
              <span class="ButtonSecondary text-center text-white text-base font-bold font-['Roboto']">
                Trouver un evenement
              </span>
            </button>
            <button
              class="ButtonPrimary md:w-80 h-12 px-6 m-2 py-2.5 bg-violet-600 rounded-lg flex justify-center items-center"
              @click="goAddEventPage">
              <span class="ButtonSecondary text-center text-white text-base font-bold font-['Roboto']">
                Proposer un evenement
              </span>
            </button>
            <button
              class="ButtonPrimary md:w-80 h-12 px-6 m-2 py-2.5 bg-violet-600 rounded-lg flex justify-center items-center"
              @click="goForumPage">
              <span class="ButtonSecondary text-center text-white text-base font-bold font-['Roboto']">
                Acceder au forum
              </span>
            </button>
          </div>
        </div>
        <img src="../assets/home.png" class="w-full md:w-1/2 mt-6 md:mt-0 flex-1" alt="Home image">
      </section>
      <FooterPage />
    </div>
  </template>
  
  <script setup lang="ts">
  import HeaderPage from '../components/Header/HeaderPage.vue';
  import FooterPage from '../components/Footer/FooterPage.vue';
  import { useRouter } from 'vue-router';
  
  const router = useRouter();
  
  const goToCityPage = () => {
    router.push({ path: '/city' });
  };
  
  const goAddEventPage = () => {
    router.push({ path: '/add' });
  };
  
  const goForumPage = () => {
    router.push({ path: '/forum' });
  };
  </script>
  
  <style>
  @tailwind base;
  @tailwind components;
  @tailwind utilities;
  
  .ButtonPrimary {
    @apply md:w-80 h-12 px-6 m-2 py-2.5 bg-violet-600 rounded-lg flex justify-center items-center;
  }
  
  .ButtonSecondary {
    @apply text-center text-white text-base font-bold font-['Roboto'];
  }
  </style>
  