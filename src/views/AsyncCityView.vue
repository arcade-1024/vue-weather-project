<template>
	<div class="flex flex-col flex-1 items-center">
		<div
			v-if="route.query.preview"
			class="text-white p-4 bg-weather-secondary w-full text-center"
		>
			<p>
				You are currently previewing this city, click the "+" icon to start
				tracking this city.
			</p>
		</div>

		<div class="flex flex-col items-center text-white py-12">
			<h1 class="text-4xl mb-2">{{ route.params.city }}</h1>
			<!-- <pre class="max-w-sm">{{
				JSON.stringify(weatherData.data.currentTime, null, 3)
			}}</pre> -->
			<p class="text-sm mb-12">
				{{
					new Date(weatherData.currentTime).toLocaleDateString("en-us", {
						weekday: "short",
						day: "2-digit",
						month: "long",
					})
				}},
				{{
					new Date(weatherData.currentTime).toLocaleTimeString("en-us", {
						timeStyle: "short",
					})
				}}
			</p>
			<p class="text-9xl">{{ Math.round(weatherData.current.temp) }}&deg;</p>
			<div class="text-center">
				<p>Feels like {{ Math.round(weatherData.current.feels_like) }}&deg;</p>
				<p class="capitalize">
					{{ weatherData.current.weather[0].description }}
				</p>
			</div>
			<img
				class="w-[150px] h-auto"
				:src="`http://openweathermap.org/img/wn/${weatherData.current.weather[0].icon}@2x.png`"
				alt=""
			/>
		</div>
		<div class="max-w-screen-md w-full">
			<div class="mx-8 text-white mb-8">
				<h2 class="mb-4 text-2xl">Hourly Weather</h2>
				<div class="flex gap-8 overflow-x-scroll pb-5 scrollBarThin">
					<div
						v-for="hourData in weatherData.hourly"
						:key="hourData.dt"
						class="flex flex-col items-center"
					>
						<p class="whitespace-nowrap text-md">
							{{
								new Date(hourData.currentTime).toLocaleTimeString("en-us", {
									hour: "numeric",
								})
							}}
						</p>
						<img
							class="w-auto h-[50px] object-cover mt-4"
							:src="`http://openweathermap.org/img/wn/${hourData.weather[0].icon}@2x.png`"
							alt=""
						/>
						<p class="text-xl">{{ Math.round(hourData.temp) }}&deg;</p>
					</div>
				</div>
			</div>
		</div>
		<button
			class="flex items-center gap-2 py-2 px-4 mt-6 mb-12 text-white cursor-pointer duration-150 hover:text-red-400 border-2 rounded-md hover:border-red-400"
			@click="removeCity"
			v-if="!route.query.preview"
		>
			<i class="fa-solid fa-trash" />
			<p>Remove city</p>
		</button>
	</div>
</template>

<script setup>
import axios from "axios";
import { useRoute, useRouter } from "vue-router";
const route = useRoute();
const router = useRouter();
const apiKey = "d0d8c6bdf7f0f2bc5ca97c322fc267d2";
const getWeatherData = async () => {
	try {
		const weatherData = await axios.get(
			`https://api.openweathermap.org/data/2.5/onecall?lat=${route.query.lat}&lon=${route.query.log}&exclude={part}&appid=7efa332cf48aeb9d2d391a51027f1a71&units=metric`
		);
		if (weatherData) {
			// cal current date & time
			const localOffset = new Date().getTimezoneOffset() * 60000;
			const utc = weatherData.data.current.dt * 1000 + localOffset;
			weatherData.data.currentTime =
				utc + 1000 * weatherData.data.timezone_offset;

			// cal hourly weather offset
			weatherData.data.hourly.forEach((hour) => {
				const utc = hour.dt * 1000 + localOffset;
				hour.currentTime = utc + 1000 * weatherData.data.timezone_offset;
			});
			console.log(weatherData);
			return weatherData.data;
		} else throw new Error();
	} catch (e) {
		console.log(e);
	}
};
const weatherData = await getWeatherData();

const removeCity = () => {
	const getCityData = JSON.parse(localStorage.getItem("savedCities"));
	const updatedCity = getCityData.filter((city) => city.id !== route.query.id);
	localStorage.setItem("savedCities", JSON.stringify(updatedCity));
	router.push({ name: "home" });
};
</script>

<style lang="scss" scoped></style>
