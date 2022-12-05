<template>
	<div class="flex py-6 px-3 rounded-md shadow-md bg-weather-secondary cursor-pointer">
		<div class="flex flex-col flex-1">
			<h1 class="text-2xl font-medium ">{{ city.city }}</h1>
			<p class="mt-1 text-gray-300">{{ city.state }}</p>
		</div>
	</div>
</template>

<script setup>
defineProps({
	city: {
		type: Object,
		default: () => {},
	},
});
</script>
