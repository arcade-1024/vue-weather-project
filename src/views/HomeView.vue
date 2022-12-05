<template>
	<main class="container text-white">
		<div class="pt-4 mb-8 relative">
			<input
				type="text"
				class="py-2 px-1 w-full bg-transparent border-b focus:outline-none focus:border-weather-secondary"
				v-model="search"
				@input="getResults"
			/>
			<ul
				v-if="mapBoxRes"
				class="absolute bg-weather-secondary text-white w-full shadow-md top-[66px] rounded-lg"
			>
				<li
					v-for="searchRes in mapBoxRes"
					:key="searchRes.id"
					class="py-2.5 px-3 cursor-pointer"
					@click="previewCity(searchRes)"
				>
					{{ searchRes.place_name }}
				</li>
			</ul>
		</div>
		<div class="flex flex-col gap-4">
			<CityList />
		</div>
	</main>
</template>
<script setup>
import { ref } from "vue";
import axios from "axios";
import { useRouter } from "vue-router";
import CityList from "../components/CityList.vue";

const router = useRouter();
const search = ref("");
const queryTimeOut = ref("");
const mapBoxRes = ref(null);
const searchError = ref(null);

const mapboxKey =
	"pk.eyJ1IjoiYXJjYWRlMTAyNCIsImEiOiJja3Y2aDFzNzEyNHJ4MzFscGgzYzBydG9xIn0.H02QqgNA2B_2XMbc5RcGVQ";
const getResults = async () => {
	clearTimeout(queryTimeOut.value);
	queryTimeOut.value = setTimeout(async () => {
		if (search.value !== "") {
			try {
				const res = await axios.get(
					`https://api.mapbox.com/geocoding/v5/mapbox.places/${search.value}.json?access_token=${mapboxKey}&types=place`
				);
				mapBoxRes.value = res.data.features;
				return;
			} catch {
				searchError.value = true;
			}
		}
		mapBoxRes.value = null;
	}, 300);
};

const previewCity = (search) => {
	const [city, state] = search.place_name.split(",");
	router.push({
		name: "cityView",
		params: { state: state.trim(), city },
		query: {
			lat: search.geometry.coordinates[1],
			log: search.geometry.coordinates[0],
			preview: true,
		},
	});
};
</script>
