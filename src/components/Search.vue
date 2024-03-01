<script setup>
import { computed, ref } from 'vue';
import { useFetch } from '@vueuse/core'; // https://vueuse.org/core/useFetch/
import { useMapStore } from '../stores/map';
import 'leaflet/dist/leaflet.css';
import L from 'leaflet';

let MapStore = useMapStore();

const query = ref();

const params = computed(() => {
  return new URLSearchParams({ q: query.value, format: 'geojson', limit: '10', namedetails: '0' });
});

const queryUrl = computed(() => {
  return `https://nominatim.openstreetmap.org/search?${params.value}`;
});

const { execute: searchRequest, isFetching, error, data: geoJSONdata } = useFetch(queryUrl, { immediate: false }).get().json();

function addPoint(data) {
  MapStore.pointData = data;
  const lat = data.geometry.coordinates[1];
  const lng = data.geometry.coordinates[0];
  MapStore.map.setView([lat, lng], 12);
  L.geoJSON(data)
    .bindPopup(data.properties.display_name)
    .addTo(MapStore.map);
  geoJSONdata.value = 0;
}

</script>

<template>
  <div class="search">
    <form v-on:submit.prevent="searchRequest">
      <input class="search--input" type="text" v-model="query">
      <button class="search--btn" type="submit">Поиск</button>
    </form>
    <div class="search--result" v-if="geoJSONdata">
      <p @click="addPoint(elem)" v-for="elem in geoJSONdata.features">{{ elem.properties.display_name }}</p>
    </div>
  </div>
  <!-- <a>{{ MapStore }}</a> -->
</template>

<style scoped></style>
