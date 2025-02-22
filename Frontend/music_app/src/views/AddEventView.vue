<template>
  <div>
    <HeaderPage></HeaderPage>
    <SearchPage @geocodeResult="handleGeocodeResult"></SearchPage>
    <ModalConfirm v-model="showError" title="Erreur" @confirm="confirmError">
      <p>{{ errorMessage }}</p>
    </ModalConfirm>
    <ModalConfirm v-model="showSuccess" title="Confirmation" @confirm="confirmSuccess">
      <p>{{ successMessage }}</p>
    </ModalConfirm>
    <section></section>
    <section class="flex flex-col lg:flex-row justify-center items-center">
      <div id="map" class="w-full lg:w-3/5 h-64 lg:h-500px" style="height: 600px;"></div>
      <div class="border-2 w-full lg:w-1/5 flex justify-center items-center flex-col bg-violet-600 rounded-md mt-4 lg:mt-0 p-4"
        v-if="result" style="height: 600px;">
        <h1 class="text-white mb-4 text-xl font-bold text-center">Ajout de l'evenement</h1>
        <label class="text-white text-m font-medium leading-tight text-center">Coordonéees GPS :</label>
        <p class="text-m text-white mb-4 text-center">Latitude : {{ result.latitude }} - Longitude : {{ result.longitude }}</p>
        <label class="text-white text-m font-medium leading-tight text-center">Adresse complète</label>
        <p class="text-m text-white mb-4 text-center">Nom de l'emplacement : {{ result.place }} {{ result.postalCode }} {{ result.city }} {{ result.country }}</p>
        <label class="text-white text-m font-medium leading-tight mb-1 text-center">Nom de l'evenement</label>
        <input class="w-full mb-4 p-2 rounded" placeholder="Saisissez le nom de l'événement" v-model="event_name" />
        <label for="music-style-select" class="mb-1 text-white text-m font-medium leading-tight text-center">Style de musique</label>
        <select class="mb-4 w-full h-12 p-2 rounded" v-model="selectedStyle" name="style" id="music-style-select">
          <option value="">Type de musique</option>
          <option value="jazz">Jazz</option>
          <option value="rap-rnb">Rap - R'N'B</option>
          <option value="classique">Classique</option>
          <option value="reggae">Reggae</option>
          <option value="pop-rock">Pop - Rock</option>
          <option value="country">Country</option>
          <option value="autre">Autres</option>
        </select>
        <label class="text-white text-m font-medium leading-tight mb-1 text-center">Numéro du groupe / de l'artiste</label>
        <input class="mb-4 w-full p-2 rounded" v-model="event_label" type="text" placeholder="Entrez le nom de l'événement" required>
        <label class="text-white text-m font-medium leading-tight mb-1 text-center">Description de l'événement</label>
        <textarea class="mb-1 w-full h-24 p-2 rounded" v-model="event_txt" type="text" placeholder="Entrez la description" required></textarea>
        <button @click="addPoint" type="submit" class="mt-1 bg-white text-violet-600 p-2 rounded hover:text-white hover:bg-violet-300 focus:outline-none focus:ring focus:ring-violet-600 focus:ring-opacity-50">
          Ajouter un point et l'événement
        </button>
      </div>
    </section>
  </div>
</template>

<script setup lang="ts">
import { ref, onMounted, computed } from 'vue';
import mapboxgl from 'mapbox-gl';
import ApiService from "@/services/ApiService";
import HeaderPage from '../components/Header/HeaderPage.vue';
import SearchPage from '../components/Map/SearchPage.vue';
import ModalConfirm from '../components/pModal/ModalConfirm.vue';

const result = ref(null);
const event_name = ref('');
const event_label= ref('');
const event_txt = ref('');
const selectedStyle = ref('');
let map;
const showError = ref(false);
const errorMessage = ref('');
const successMessage = ref('');
const showSuccess = ref(false);

const confirmError = () => {
    showError.value = false;
};

const confirmSuccess = () => {
    showSuccess.value = false;
};

const styleColor = computed(() => {
  switch (selectedStyle.value) {
    case 'jazz':
      return '#00FF7F';
    case 'rap-rnb':
      return '#6A5ACD';
    case 'classique':
      return '#FFC0CB';
    case 'reggae':
      return '#D2691E';
    case 'pop-rock':
      return '#FFE4E1';
    case 'country':
      return '#B0C4DE';
    default:
      return '#000000';
  }
});

const handleGeocodeResult = (geocodeResult) => {
  result.value = geocodeResult;
  updateMap([geocodeResult.longitude, geocodeResult.latitude]);
};

const addPoint = async () => {
  try {
    if (!result.value) {
      throw new Error("Aucune donnée de géocodage disponible.");
    }

    const Point = {
      longitude: result.value.longitude,
      latitude: result.value.latitude,
      text: event_name.value,
      address: result.value.place,
      insee_code: 77270,
      city_name: result.value.city,
      zip_code: result.value.postalCode,
      style: selectedStyle.value,
      label: event_label.value,
      color: styleColor.value,
      departement_name: "Paris",
      departement_number: result.value.postalCode,
      region_name: result.value.country,
      region_geo_json: ""
    };

    const Event = {
      name: event_name.value,
      description: event_txt.value,
      url: "",
    };

    const authToken = await getAuthToken();

    const response_event = await ApiService.post('/event/add-event', Event, {
      headers: {
        Authorization: `Bearer ${authToken}`,
      },
    });

    const eventId = response_event.data.id;
    const event_url = `http://localhost:5173/event/${eventId}`;
    Point.region_geo_json = event_url;

    const response = await ApiService.post('/city/add-point', Point, {
      headers: {
        Authorization: `Bearer ${authToken}`,
      },
    });

    console.log(response.data);
    successMessage.value = "Le point a bien été ajouté";
    showSuccess.value = true;
  } catch (error: any) {
    console.error(error);
    errorMessage.value = error.response?.data?.message || error.message;
    showError.value = true;
  }
};

const getAuthToken = async () => {
  return localStorage.getItem('authToken');
};

const updateMap = (coordinates) => {
  if (map) {
    map.remove();
  }

  map = new mapboxgl.Map({
    container: 'map',
    style: 'mapbox://styles/mapbox/streets-v12',
    center: coordinates,
    zoom: 15,
  });

  new mapboxgl.Marker()
    .setLngLat(coordinates)
    .addTo(map);
};

onMounted(() => {
  const token: string = import.meta.env.VITE_MAPBOX_TOKEN;
  mapboxgl.accessToken = token;

});
</script>

<style>
@tailwind base;
@tailwind components;
@tailwind utilities;
</style>
