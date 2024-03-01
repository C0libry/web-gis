<script setup>
import { computed, onMounted, ref } from 'vue';
import { useFetch } from '@vueuse/core'; // https://vueuse.org/core/useFetch/
import 'leaflet/dist/leaflet.css';
import L from 'leaflet';
import { useMapStore } from '../stores/map';
defineProps({
  msg: {
    type: String,
    required: true
  }
})

const MapStore = useMapStore();

const lat = ref();
const lng = ref();
const map = ref();
const mapContainer = ref();

let yandexSatellite;
let yandexMap;

const SEVASTOPOL = [44.556972, 33.526402];

const query = ref();

const params = computed(() => {
  return new URLSearchParams({ q: query.value, format: 'geojson', limit: '10', namedetails: '0' });
});

const queryUrl = computed(() => {
  return `https://nominatim.openstreetmap.org/search?${params.value}`;
});

const { execute: searchRequest, isFetching, error, data: geoJSONdata } = useFetch(queryUrl, { immediate: false }).get().json();

// const data = computed(() => { addPoint(MapStore.pointData) });

// function addPoint(data) {
//   const lat = data.geometry.coordinates[1];
//   const lng = data.geometry.coordinates[0];
//   MapStore.map.setView([lat, lng], 12);
//   L.geoJSON(data)
//     .bindPopup(data.properties.display_name)
//     .addTo(MapStore.map);
//   geoJSONdata.value = 0;
// }

onMounted(() => {
  MapStore.map = L.map(mapContainer.value).setView(SEVASTOPOL, 10);

  yandexSatellite = L.tileLayer('https://core-sat.maps.yandex.net/tiles?l=sat&v=3.1025.0&x={x}&y={y}&z={z}&scale=1&lang=ru_RU', {
    maxZoom: 19,
  });
  yandexSatellite.addTo(MapStore.map);

  yandexMap = L.tileLayer(
    'https://core-renderer-tiles.maps.yandex.net/tiles?l=map&x={x}&y={y}&z={z}&scale=1&lang=ru_RU', {
    maxZoom: 21,
    crs: L.CRS.EPSG3395,
    attribution: '&copy; <a href="http://www.yandex.ru">Яндекс</a>',
  });
  yandexMap.addTo(MapStore.map);
  
  // EPSG3857 - defalt
  // EPSG3395 - yandex map
  MapStore.map.options.crs = L.CRS.EPSG3395;

  // var point = L.marker(SEVASTOPOL, {draggable: true}).addTo(MapStore.map);

  // var point = L.marker([44.596972, 33.526402], {draggable: true}).addTo(MapStore.map).rempm;
  // point.remove();

  // let boundary = L.geoJSON(MapStore.boundaryPolygon, {
  //   style: {
  //     color: "#ff7800",
  //     opacity: 0.5,
  //     fillOpacity: 0
  //   },
  //   // onEachFeature: function(feature, elem){
  //   //   console.log(feature.properties.NAME)
  //   //   elem.bindPopup(feature.properties.NAME)}
  // });

  // let pointOfInterest = L.geoJSON(MapStore.pointOfInterest, {
  //   style: {
  //     opacity: 0.5,
  //     fillOpacity: 0.1
  //   },
  //   onEachFeature: function (feature, elem) {
  //     let popupData = ""
  //     for (let key in feature.properties) {
  //       let value = feature.properties[key];
  //       if (value !== "" && value !== null && key != 'NAME_EN' && key != 'NAME_RU' && key != 'OSM_TYPE' && key != 'OSM_ID') {
  //         popupData += key + ": " + value + ", ";
  //       }
  //     }
  //     elem.bindPopup(popupData)
  //   }
  // })
  //   .addTo(MapStore.map);

  let shop = L.geoJSON(MapStore.pointOfInterest.features.filter(d => d.properties.SHOP != null), {
    style: {
      color: "#806491",
      opacity: 0.5,
      fillOpacity: 0.1
    },
    onEachFeature: function (feature, elem) {
      let popupData = ""
      for (let key in feature.properties) {
        let value = feature.properties[key];
        if (value !== "" && value !== null && key != 'NAME_EN' && key != 'NAME_RU' && key != 'OSM_TYPE' && key != 'OSM_ID') {
          popupData += key + ": " + value + ", ";
        }
      }
      elem.bindPopup(popupData)
    }
  });

  let sport = L.geoJSON(MapStore.pointOfInterest.features.filter(d => d.properties.SPORT != null), {
    style: {
      color: "#8ed2b9",
      opacity: 0.5,
      fillOpacity: 0.1
    },
    onEachFeature: function (feature, elem) {
      let popupData = ""
      for (let key in feature.properties) {
        let value = feature.properties[key];
        if (value !== "" && value !== null && key != 'NAME_EN' && key != 'NAME_RU' && key != 'OSM_TYPE' && key != 'OSM_ID') {
          popupData += key + ": " + value + ", ";
        }
      }
      elem.bindPopup(popupData)
    }
  });

  let leisure = L.geoJSON(MapStore.pointOfInterest.features.filter(d => d.properties.LEISURE != null), {
    style: {
      color: "#EDC5AB",
      opacity: 0.5,
      fillOpacity: 0.1
    },
    onEachFeature: function (feature, elem) {
      let popupData = ""
      for (let key in feature.properties) {
        let value = feature.properties[key];
        if (value !== "" && value !== null && key != 'NAME_EN' && key != 'NAME_RU' && key != 'OSM_TYPE' && key != 'OSM_ID') {
          popupData += key + ": " + value + ", ";
        }
      }
      elem.bindPopup(popupData)
    }
  });

  let tourism = L.geoJSON(MapStore.pointOfInterest.features.filter(d => d.properties.TOURISM != null), {
    style: {
      color: "#c3b21e",
      opacity: 0.5,
      fillOpacity: 0.1
    },
    onEachFeature: function (feature, elem) {
      let popupData = ""
      for (let key in feature.properties) {
        let value = feature.properties[key];
        if (value !== "" && value !== null && key != 'NAME_EN' && key != 'NAME_RU' && key != 'OSM_TYPE' && key != 'OSM_ID') {
          popupData += key + ": " + value + ", ";
        }
      }
      elem.bindPopup(popupData)
    }
  });

  let baseMaps = {
    "Яндекс спутнкиовая карта": yandexSatellite,
    "Яндекс карта": yandexMap,
    // "Google": googleStreets,
  };

  let overlayMaps = {
    // "Полигон границ": L.layerGroup([boundary]),
    // "Точки интереса": L.layerGroup([pointOfInterest]),
    "Магазины": L.layerGroup([shop]),
    "Спорт": L.layerGroup([sport]),
    "Досуг": L.layerGroup([leisure]),
    "Туризм": L.layerGroup([tourism]),
  };
  let layerControl = L.control.layers(baseMaps, overlayMaps, { collapsed: false }).addTo(MapStore.map);
});

// function getLocation() {
//   if (navigator.geolocation) {
//     navigator.geolocation.getCurrentPosition((position) => {
//       lat.value = position.coords.latitude;
//       lng.value = position.coords.longitude;
//       MapStore.map.setView([lat.value, lng.value], 13);
//       L.marker([lat.value, lng.value]).addTo(MapStore.map);
//     });
//   }
// }
</script>

<template>
  <!-- <button @click="getLocation">Get location</button> -->
  <!-- <div>{{ lat }}, {{ lng }}</div> -->

  <div class="map" ref="mapContainer"></div>
</template>

<style scoped></style>
