<template>
  <div class="marker" :style="markerStyle" @click="selectAirport">
    <div class="marker-dot"></div>
    <div class="marker-label">{{ airport.code }}</div>
  </div>
</template>

<script>
export default {
  name: 'Globe-Marker',
  props: {
    airport: {
      type: Object,
      required: true
    }
  },
  computed: {
    markerStyle() {
      // Calculate position based on airport coordinates
      return {
        left: `${this.airport.x}px`,
        top: `${this.airport.y}px`
      }
    }
  },
  methods: {
    selectAirport() {
      this.$emit('select', this.airport)
    }
  }
}
</script>

<style scoped>
.marker {
  position: absolute;
  cursor: pointer;
}

.marker-dot {
  width: 8px;
  height: 8px;
  background-color: red;
  border-radius: 50%;
}

.marker-label {
  font-size: 12px;
  margin-top: 2px;
}
</style>
