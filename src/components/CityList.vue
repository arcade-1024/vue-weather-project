<template>
	<div v-for="city in savedCites" :key="city.id">
		<CityCard :city="city" @click="gotoCity(city)" />
	</div>
	<div v-if="savedCites.length === 0">No location added</div>
</template>

<script setup>
import { ref } from "vue";
import { useRouter } from "vue-router";
import CityCard from "./CityCard.vue";

const router = useRouter();
const savedCites = ref([]);
const getCities = () => {
	const getCityData = localStorage.getItem("savedCities");
	if (getCityData) {
		savedCites.value = JSON.parse(getCityData);
	}
};
getCities();

const gotoCity = (city) => {
	router.push({
		name: "cityView",
		params: { state: city.state, city: city.city },
		query: { lat: city.coords.lat, log: city.coords.log, id: city.id },
	});
};
</script>
