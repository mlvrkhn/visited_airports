<template>
  <div class="search-bar">
    <input 
      v-model="searchQuery" 
      @input="handleInput"
      placeholder="Search for airports..."
    >
  </div>
</template>

<script>
export default {
  name: 'SearchBar',
  data() {
    return {
      searchQuery: ''
    }
  },
  methods: {
    handleInput() {
      this.$emit('search', this.searchQuery)
    }
  }
}
</script>

<style scoped>
.search-bar {
  position: relative;
  margin: 20px;
  z-index: 10;
}

input {
  width: 300px;
  padding: 10px;
  font-size: 16px;
  border: 1px solid #ccc;
  border-radius: 4px;
}
</style>
