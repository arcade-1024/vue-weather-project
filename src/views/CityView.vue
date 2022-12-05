<template>
	<div>
		<Suspense>
			<AsyncCityView />
			<template #fallback> </template>
		</Suspense>
	</div>
</template>

<script setup>
import AsyncCityView from "./AsyncCityView.vue";
</script>

<style scoped></style>
