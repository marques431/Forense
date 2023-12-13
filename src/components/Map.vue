<template>
	<div>
		<div class="inputs">

			<button v-if="markers.length > 0" @click="addRoutingControl()"  class="bg-gray-300 hover:bg-gray-400 text-gray-800 font-bold py-2 px-4 rounded inline-flex items-center mb-2">
				<svg xmlns="http://www.w3.org/2000/svg" height="16" width="12" viewBox="0 0 384 512"><path d="M215.7 499.2C267 435 384 279.4 384 192C384 86 298 0 192 0S0 86 0 192c0 87.4 117 243 168.3 307.2c12.3 15.3 35.1 15.3 47.4 0zM192 128a64 64 0 1 1 0 128 64 64 0 1 1 0-128z"/></svg>
				<span class="px-2">Show route</span>
			</button>
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
import 'leaflet-routing-machine/dist/leaflet-routing-machine.css';
import 'leaflet-routing-machine/dist/leaflet-routing-machine';

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
			this.showMap();
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

			if(typeof marker.exifData.latitude !== 'undefined' && typeof marker.exifData.longitude !== 'undefined'){
				let hours = null;
				if(typeof marker.exifData.GPSTimeStamp !== 'undefined'){
					hours = "<br>" + marker.exifData.GPSTimeStamp;
				}
				let m = L.marker([marker.exifData.latitude, marker.exifData.longitude])
				.bindPopup("<b>" + marker.name + "</b>" + hours)
				.addTo(this.mapInstance);
				
				this.markersLayer.push(m);
			}
		},
		addRoutingControl() {
			/*let aaa = [
				{ lat: 51.5, lng: -0.09 },
				{ lat: 51.51, lng: -0.1 },
				{ lat: 51.52, lng: -0.11 }
			]*/
/*
			this.markers.forEach(marker => {
				L.marker([marker.exifData.latitude, marker.exifData.longitude]).addTo(this.mapInstance);
			});*/
			
			L.Routing.control({
				waypoints: this.markers.map(marker => L.latLng(marker.exifData.latitude, marker.exifData.longitude)),
				routeWhileDragging: true,
			}).addTo(this.mapInstance);
		}
	},
};
</script>