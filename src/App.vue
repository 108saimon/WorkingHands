<script setup>
import Calendar from './components/Calendar.vue';
</script>

<template>
  <div class="wrapper">
    <div class="container">
      <h1>Календарь</h1>
      <div>
        <Calendar />
      </div>
    </div>
  </div>
</template>

<style>
body, html {
  min-height: 100vh;
}
div {
  box-sizing: border-box;
}
.wrapper {
  display: flex;
  justify-content: center;
  align-items: center;
}
.container {
  width: 960px;
  padding: 20px;
}
</style>
