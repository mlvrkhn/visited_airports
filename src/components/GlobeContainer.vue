<template>
  <div class="globe-container">
    <div class="content-wrapper">
      <div class="search-bar-container">
        <SearchBar @search="handleSearch" />
      </div>
      <div class="globe">
        <GlobeCanvas />
      </div>
    </div>
    <Marker v-for="airport in airports" :key="airport.id" :airport="airport" />
    <AirportInfoPanel :selectedAirport="selectedAirport" />
  </div>
</template>

<script>
import SearchBar from './SearchBar.vue'
import Marker from './GlobeMarker.vue'
import AirportInfoPanel from './AirportInfoPanel.vue'
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
  overflow: hidden;
}

.content-wrapper {
  display: flex;
  align-items: center;
  justify-content: center;
  height: 100%;
}

.search-bar-container {
  padding: 0 20px;
}

.globe {
  flex-grow: 1;
  width: 100%;
}

@media (min-width: 768px) {
  .content-wrapper {
    flex-direction: row;
  }

  .search-bar-container {
    width: 30%;
  }

  .globe {
    width: 70%;
  }
}

@media (max-width: 767px) {
  .content-wrapper {
    flex-direction: column;
  }

  .search-bar-container {
    padding: 20px 0;
  }
}
</style>
