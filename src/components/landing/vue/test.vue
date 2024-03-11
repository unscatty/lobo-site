<template>
  <div class="container">
    <div
      v-for="(col, index) in columns"
      :key="index"
      :style="{ width: col.width }"
      class="column"
      @mouseenter="expandColumn(index)"
      @mouseleave="resetColumns"
    >
      {{ col.content }}
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      // Initial widths and content for each column
      columns: [
        { width: '100px', content: 'Column 1' },
        { width: '100px', content: 'Column 2' },
        { width: '100px', content: 'Column 3' },
        { width: '100px', content: 'Column 4' },
        { width: '100px', content: 'Column 5' },
        { width: '100px', content: 'Column 6' },
      ],
    }
  },
  methods: {
    expandColumn(index) {
      // Double the width of the hovered column
      this.columns[index].width = '200px'
      // Adjust the widths of other columns
      const otherWidth = (400 - 200) / 3 + 'px' // Total width minus expanded column divided by number of other columns
      this.columns.forEach((col, i) => {
        if (i !== index) {
          col.width = otherWidth
        }
      })
    },
    resetColumns() {
      // Reset all column widths to original
      this.columns.forEach((col) => {
        col.width = '100px'
      })
    },
  },
}
</script>

<style scoped>
.container {
  display: flex; /* or as needed */
  align-items: center;
  overflow: hidden;
  width: 400px; /* Adjust based on your total available width */
  transition: width 0.5s; /* Optional */
}

.column {
  display: inline-block; /* Adjust as needed */
  height: 100px; /* Example height */
  background-color: lightblue;
  margin: 0px;
  transition: width 0.5s ease; /* Smooth width transition */
}
</style>
