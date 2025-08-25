<script setup>
import Calendar from './components/Calendar.vue';

function alertDate(payload) {
  alert(`Дата: ${payload.date}; Отформатированная дата: ${payload.formattedDate}; `)
}
</script>

<template>
  <div class="wrapper">
    <div class="container">
      <h1>Пример использования:</h1>
      <div>
        <p>Календарь с дефолтными настройками</p>
        <Calendar @date-selected="alertDate" />
      </div>
      <!-- <br> -->
      <div>
        <p>Календарь с предустановленными настройками(дата: 13 мая 2007; язык - английский)</p>
        <Calendar
          :initial-date="'2007-5-13'"
          :language="'en'"
          @date-selected="alertDate"
        />
      </div>
    </div>
  </div>
</template>

<style>
body, html {
  min-height: 100vh;
  font-family: sans-serif;
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
