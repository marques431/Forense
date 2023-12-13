<script setup lang="ts">
import ImageUploader from './components/ImageUploader.vue';
import Map from './components/Map.vue';
</script>

<template>
	<main class="flex flex-col items-center justify-center h-screen">
		<h1 class="text-white text-4xl">Forensic Metadata Analysis</h1>
		<div class="flex w-full p-5 space-x-5 h-[80vh]">
			<div class="w-1/2">
				<ImageUploader @sendData="receiveData"/>
			</div>
			<div class="w-1/2">
				<Map :markers="markers" class="w-full" />
			</div>
		</div>
	</main>
</template>

<script lang="ts">
export default {
	data() {
		return {
			markers: []
		};
	},
	methods: {
		receiveData(markers) {
		this.markers = markers;
		console.log(this.markers);
		
		}
	},
	components: {
		ImageUploader,
		Map,
	},
};
</script>