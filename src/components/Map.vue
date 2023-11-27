<template>
	<div>
		<div class="inputs">
			<!--
			<label for="latitude">Latitude:</label>
			<input v-model="latitude" id="latitude" type="text" placeholder="Enter latitude" />
			
			<label for="longitude">Longitude:</label>
			<input v-model="longitude" id="longitude" type="text" placeholder="Enter longitude" />
			
			<button @click="showMap">Show Map</button>
			<button @click="addMarker">Add Marker</button>
			-->
		</div>
		<div class="map flex-grow w-full h-[75vh] z-[1]" ref="map"></div>
	</div>
</template>

<script>

import L from 'leaflet';
	import 'leaflet/dist/leaflet.css';

export default {
	props: {
		markers: [],
	},
	data() {
	  return {
		latitude: '40.9400723',
		longitude: '-8.6600396',
		mapInstance: null,
		markersLayer: []
	  };
	},
	mounted() {
		this.showMap();
	},
	watch: {
		markers(newData) {
			for (var i = 0; i < this.markersLayer.length; i++) {
				this.mapInstance.removeLayer(this.markersLayer[i]);
			}
			this.markersLayer = [];

			newData.forEach((marker) => {
				this.addMarker(marker);
			});
		},
  },
	methods: {
		async showMap() {
			// You can add additional validation for latitude and longitude here
			if (this.latitude && this.longitude) {
				if (!this.mapInstance) {
					await this.initializeMapAsync();
				}

				const location = [parseFloat(this.latitude), parseFloat(this.longitude)];
				//this.mapInstance.setView(location, 15);

				/*if (this.marker) {
					console.log('Moving Marker to:', location);
					this.marker.setLatLng(location);
				} else {
					console.log('Creating Marker at:', location);
					this.marker = L.marker(location).addTo(this.mapInstance);
				}*/
			}
		},
		initializeMapAsync() {
			return new Promise((resolve) => {
				this.mapInstance = L.map(this.$refs.map).setView([0, 0], 2);

				L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png', {
					attribution: '&copy; <a href="https://www.openstreetmap.org/copyright">OpenStreetMap</a> contributors',
				}).addTo(this.mapInstance);

				// Resolve the promise once the map is initialized
				resolve();
			});
		},
		addMarker(marker){
			let greenIcon = L.icon({
				iconUrl: 'leaf-green.png',
				shadowUrl: 'leaf-shadow.png',

				iconSize:     [38, 95], // size of the icon
				shadowSize:   [50, 64], // size of the shadow
				iconAnchor:   [22, 94], // point of the icon which will correspond to marker's location
				shadowAnchor: [4, 62],  // the same for the shadow
				popupAnchor:  [-3, -76] // point from which the popup should open relative to the iconAnchor
			});

			let m = L.marker([marker.exifData.latitude, marker.exifData.longitude], {icon: greenIcon})
			.bindPopup("<b>"+marker.name+"</b><br>"+marker.exifData.GPSTimeStamp)
			.addTo(this.mapInstance);

			this.markersLayer.push(m);
		},
	},
};
</script>