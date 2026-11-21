<script setup>
import { onMounted, onBeforeUnmount, ref } from "vue";
import L from "leaflet";
import "leaflet/dist/leaflet.css";

const mapElement = ref(null);
const props = defineProps({
    latitude: Number, longitude: Number, name: String
});
let map;
onMounted(() => {
map = L.map(mapElement.value).setView([props.latitude, props.longitude], 12);
    L.tileLayer("https://tile.openstreetmap.org/{z}/{x}/{y}.png", {
        attribution: '&copy; OpenStreetMap contributors',
        maxZoom: 19
    }).addTo(map);
    L.circle([props.latitude, props.longitude], { 
        radius: 150,
        color: "#c0392b"
    })
        .addTo(map).bindPopup(`${props.name} (${props.latitude}, ${props.longitude})`);
        });
onBeforeUnmount(() => { if (map) map.remove();});

</script>

<template><div ref="mapElement" class="map-demo"></div></template>
<style scoped>.map-demo { height: 300px; width: 100%; }</style>

