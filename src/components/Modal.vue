<template>
	<div v-if="isOpen" class="fixed inset-0 bg-gray-500 bg-opacity-75 flex items-center justify-center text-black z-10">
		<div class=" bg-gray-400 p-5 rounded shadow-md h-3/4">
			<div class="flex justify-between mb-4">
				<h2 class="text-2xl font-bold">EXIF Data</h2>
				<button @click="closeModal" class="text-gray-600 hover:text-gray-800 focus:outline-none">
					<svg class="h-6 w-6" fill="none" stroke="currentColor" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg">
						<path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12"></path>
					</svg>
				</button>
			</div>
			<div class="overflow-y-auto h-9/10">
				<div v-if="exifData">
					<ul class="list-disc">
						<li v-for="(value, key) in exifData" :key="key">
							<strong>{{ key }}:</strong> {{ value }}
						</li>
					</ul>
				</div>
			</div>
		</div>
	</div>
</template>

<script>
export default {
	data() {
		return {
			isOpen: false,
			exifData: []
		};
	},
	methods: {
		openModal(exifData) {
			this.exifData = exifData;
			this.isOpen = true;
		},
		closeModal() {
			this.isOpen = false;
		},
	},
};
</script>