<template>
    <div class="molecule-color-selector">
      <label for="colorPicker">Select Color:</label>
      <input type="color" id="colorPicker" v-model="selectedColor" @input="onColorChange" />
    </div>
  </template>
  
  <script>
  export default {
    props: {
      value: String,
    },
    data() {
      return {
        selectedColor: this.value || "#FFFFFF",
      };
    },
    methods: {
    //   onColorChange() {
    //     this.$emit("input", this.selectedColor);
    //     this.$emit("activeColor", this.selectedColor);
    //   },
    },
  };
  </script>
  
  <style scoped>
  .molecule-color-selector {
    margin: 10px;
  }
  </style>
  