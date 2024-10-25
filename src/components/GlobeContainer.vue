<template>
  <div class="globe-container">
    <SearchBar @search="handleSearch" />
    <div class="globe">
    <GlobeCanvas />
    </div>
    <Marker v-for="airport in airports" :key="airport.id" :airport="airport" />
    <AirportInfoPanel :selectedAirport="selectedAirport" />
  </div>
</template>

<script>
import SearchBar from './SearchBar.vue'
import Marker from './GlobeMarker.vue'
import AirportInfoPanel from './AirportInfoPanel.vue'
import countries from '@/assets/countries-110m.json' // Import countries data
import GlobeCanvas from './GlobeCanvas.vue'
export default {
  name: 'GlobeContainer',
  components: {
    SearchBar,
    Marker,
    AirportInfoPanel,
    GlobeCanvas
  },
  data() {
    return {
      airports: [],
      selectedAirport: null,
      // world: {
      //   type: "Topology",
      //   objects: {
      //     countries: countries, // Use imported countries data
      //     land: {}
      //   },
      //   arcs: [],
      //   bbox: [-180, -85.60903777459771, 180, 83.64513000000001],
      //   transform: {
      //     scale: [],
      //     translate: []
      //   }
      // }
    }
  },
  methods: {
    handleSearch(query) {
      console.log('Search query:', query)
      // Implement search functionality
    }
  }
}
</script>

<style scoped>
.globe-container {
  position: relative;
  width: 100%;
  height: 100vh;
}

.globe {
  width: 100%;
  height: 100%;
}
</style>
