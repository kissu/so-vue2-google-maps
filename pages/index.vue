<template>
  <div>
    <button class="blue white--text" @click="getCurrentPositionandBathroom">
      search for bathroom!
    </button>
    <GmapMap
      ref="mapRef"
      :center="maplocation"
      :zoom="15"
      map-type-id="roadmap"
      style="height: 300px; width: 900px"
    >
      <GmapMarker
        v-for="m in markers"
        :key="m.id"
        :position="m.position"
        :clickable="true"
        :draggable="true"
        :icon="m.icon"
        @click="center = m.position"
      />
    </GmapMap>
  </div>
</template>

<script>
import { gmapApi } from 'vue2-google-maps'

export default {
  data() {
    return {
      maplocation: { lat: 35.6814366, lng: 139.767157 },
      markers: [],
    }
  },
  computed: {
    google: gmapApi,
  },
  methods: {
    getCurrentPositionandBathroom() {
      if (process.client) {
        if (!navigator.geolocation) {
          alert('Japanese sentences')
          return
        }
        navigator.geolocation.getCurrentPosition(this.success, this.error)
      }
    },
    success(position) {
      this.maplocation.lat = position.coords.latitude
      this.maplocation.lng = position.coords.longitude
      // this.$gmapApiPromiseLazy().then(() => { // not needed here anymore
      this.google.maps.event.addListenerOnce(
        this.$refs.mapRef.$mapObject,
        'idle',
        function () {
          this.getBathroom()
        }.bind(this)
      )
      // })
    },
    getBathroom() {
      const map = this.$refs.mapRef.$mapObject
      const placeService = new this.google.maps.places.PlacesService(map)
      placeService.nearbySearch(
        {
          location: new this.google.maps.LatLng(
            this.maplocation.lat,
            this.maplocation.lng
          ),
          radius: 500,
          type: ['convenience_store'],
        },
        function (results, status) {
          if (status === this.google.maps.places.PlacesServiceStatus.OK) {
            results.forEach((place) => {
              const icon = {
                url: place.icon,
                scaledSize: new this.google.maps.Size(30, 30),
              }
              const marker = {
                position: place.geometry.location,
                icon,
                title: place.name,
                id: place.place_id,
              }
              this.markers.push(marker)
            })
          }
        }.bind(this)
      )
    },
    error(errorMessage) {
      switch (errorMessage.code) {
        case 1:
          alert('Japanese sentences')
          break
        case 2:
          alert('Japanese sentences')
          break
        case 3:
          alert('Japanese sentences')
          break
        default:
          alert('Japanese sentences')
          break
      }
    },
  },
}
</script>
