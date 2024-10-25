<template>
  <div v-if="selectedAirport" class="airport-info-panel">
    <h2>{{ selectedAirport.name }}</h2>
    <p>Code: {{ selectedAirport.code }}</p>
    <p>City: {{ selectedAirport.city }}</p>
    <p>Country: {{ selectedAirport.country }}</p>
    <!-- Add more airport information as needed -->
  </div>
</template>

<script>
export default {
  name: 'AirportInfoPanel',
  props: {
    selectedAirport: {
      type: Object,
      default: null
    }
  }
}
</script>

<style scoped>
.airport-info-panel {
  position: absolute;
  bottom: 20px;
  right: 20px;
  background-color: white;
  padding: 20px;
  border-radius: 8px;
  box-shadow: 0 2px 10px rgba(0, 0, 0, 0.1);
  max-width: 300px;
}

h2 {
  margin-top: 0;
}
</style>
