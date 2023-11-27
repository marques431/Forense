<script setup lang="ts">
import Modal from './Modal.vue';
import EXIFR from 'exifr';
</script>

<template>
	<div>
		<div class="flex items-center justify-center w-full py-5">
			<label for="fileInput" class="flex flex-col items-center justify-center w-full h-48 border-2 border-gray-300 border-dashed rounded-lg cursor-pointer bg-gray-50 dark:hover:bg-bray-800 dark:bg-gray-700 hover:bg-gray-100 dark:border-gray-600 dark:hover:border-gray-500 dark:hover:bg-gray-600">
				<div class="flex flex-col items-center justify-center pt-5 pb-6">
					<svg class="w-8 h-8 mb-4 text-gray-500 dark:text-gray-400" aria-hidden="true" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 20 16">
						<path stroke="currentColor" stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M13 13h3a3 3 0 0 0 0-6h-.025A5.56 5.56 0 0 0 16 6.5 5.5 5.5 0 0 0 5.207 5.021C5.137 5.017 5.071 5 5 5a4 4 0 0 0 0 8h2.167M10 15V6m0 0L8 8m2-2 2 2"/>
					</svg>
					<p class="mb-2 text-sm text-gray-500 dark:text-gray-400"><span class="font-semibold">Click to upload</span> or drag and drop</p>
					<p class="text-xs text-gray-500 dark:text-gray-400">JPG, JPEG, PNG, GIF, TIFF or .SVG</p>
				</div>
				<input id="fileInput" ref="fileInput" type="file" class="hidden" @change="handleFileChange" multiple accept=".jpeg,.jpg,.png,.gif,.tiff,.svg"/>
			</label>
		</div> 

		<table class="w-full text-center text-white" v-if="images.length > 0">
			<tbody>
				<tr v-for="(image, index) in images" :key="index">
					<td>
						<img :src="image.preview" class="m-auto max-h-40 max-w-40 p-2" />
					</td>
					<td>
						{{ image.name }}
					</td>
					<td class=" w-fit">
						<button @click="openModal(image.exifData)" class="bg-gray-500 text-white px-4 py-2 rounded">Additional info</button>
					</td>
				</tr>
			</tbody>
		</table>
		<div class="flex justify-around text-white" v-else>
			No images uploaded
		</div>

		<Modal ref="modalRef" />

	</div>
</template>
  
<script lang="ts">

export default {
	data() {
		return {
			images: []
		};
	},
	methods: {
		async handleFileChange() {
			const files = this.$refs.fileInput.files;

			// Clear existing images
			this.images = [];

			for (let i = 0; i < files.length; i++) {
				const file = files[i];
				const preview = URL.createObjectURL(file);
				const exifData = await this.getExifData(file);

				this.images.push({
					name: file.name,
					preview,
					exifData,
				});
			}

			this.$emit('sendData', this.images);
		},
		async getExifData(file) {
			try {
				const exifData = await EXIFR.parse(file);
				return exifData;
			} catch (error) {
				console.error('Error parsing EXIF data:', error);
				return null;
			}
		},
		openModal(exifData) {
			console.log(exifData);
			this.selectedImageExifData = exifData;
			this.$refs.modalRef.openModal(exifData);
		},
	}
};
</script>

<style lang="postcss" scoped>
	table, td{
		@apply border border-collapse;
	}
</style>
  