<script setup>
import { onMounted, ref, watch } from "vue";
import { useRoute } from "vue-router";
import { API_URL } from "../config";
import SchoolLocation from "../components/SchoolLocationMap.vue";


const route = useRoute();

const school = ref(null);
const loading = ref(false);
const errorMessage = ref("");
const schoolLocation = ref(null);
const schoolLocationLoading = ref(false);
const schoolLocationErrorMessage = ref("");



async function loadSchool() {
  loading.value = true;
  errorMessage.value = "";
  school.value = null;

  try {
    const response = await fetch(
      `${API_URL}/schools/${route.params.id}`
    );
    if (!response.ok) {
      throw new Error(`HTTP error: ${response.status}`);
    }
    school.value = await response.json();
  } catch (error) {
      errorMessage.value = error.message;
  } finally {
      loading.value = false;
  }
}

async function loadSchoolLocation() {
  schoolLocationLoading.value = true;
  schoolLocationErrorMessage.value = "";
  schoolLocation.value = null;

  try {
    const response = await fetch(
      `${API_URL}/schools/${route.params.id}/location`
    );
    if (response.status === 404) {
      throw new Error("School Location Unavailable");
    }
    if (!response.ok) {
      throw new Error(`HTTP error: ${response.status}`);
    }
    schoolLocation.value = await response.json();
  } catch (error) {
      schoolLocationErrorMessage.value = error.message;
  } finally {
      schoolLocationLoading.value = false;
  }
}

onMounted(loadSchool);
onMounted(loadSchoolLocation);

watch(
  () => route.params.id,
  () => {
    loadSchool();
    loadSchoolLocation();
  }
);

</script>

<template>
  <main class="page">
    <RouterLink to="/schools">Back to schools</RouterLink>

    <p v-if="loading">Loading school...</p>

    <p v-else-if="errorMessage" class="error">
      {{ errorMessage }}
    </p>

    <section v-else-if="school" class="school-detail">
      <h1>{{ school.name_th }}</h1>
      <p>Province: {{ school.province_name_th }}</p>
      <p>District: {{ school.district_name_th }}</p>
      <p>Subdistrict: {{ school.subdistrict_name_th }}</p>
      <p>SMIS code: {{ school.smis_code }}</p>
      <p v-if="school.name_en">English name: {{ school.name_en }}</p>
      <h2>School Location</h2>
      <p v-if="schoolLocationLoading">Loading location...</p>
      <p v-else-if="schoolLocationErrorMessage" class="error">
        {{ schoolLocationErrorMessage }}
      </p>
      <SchoolLocation
        v-if="schoolLocation"
        :name="school.name_th"
        :latitude="schoolLocation.latitude"
        :longitude="schoolLocation.longitude"
      />
    </section>
  </main>
</template>
