<script setup>
import { defineProps, ref, computed, onMounted } from 'vue';

const locales = {
  ru: {
    months: [
      'Январь',
      'Февраль',
      'Март',
      'Апрель',
      'Май',
      'Июнь',
      'Июль',
      'Август',
      'Сентябрь',
      'Октябрь',
      'Ноябрь',
      'Декабрь'
    ],
    weekDays: ['Пн', 'Вт', 'Ср', 'Чт', 'Пт', 'Сб', 'Вс']
  },
  en: {
    months: [
      'January',
      'February',
      'March',
      'April',
      'May',
      'June',
      'July',
      'August',
      'September',
      'October',
      'November',
      'December'
    ],
    weekDays: ['Sun', 'Mon', 'Tue', 'Wed', 'Thu', 'Fri', 'Sat']
  }
};

const props = defineProps({
  initialDate: {
    type: String,
    default: null
  },
  language: {
    type: String,
    default: 'ru'
  }
});

const currentDate = ref(new Date());
const selectedDate = ref(null);
const currentLanguage = ref(props.language);

const currentYear = computed(() => {
  return currentDate.value.getFullYear();
});

const currentMonth = computed(() => {
  return currentDate.value.getMonth();
});

const currentMonthName = computed(() => {
  return locales[currentLanguage.value].months[currentMonth.value];
});

const weekDays = computed(() => {
  return locales[currentLanguage.value].weekDays;
});

const calendarDays = computed(() => {
  const year = currentYear.value
  const month = currentMonth.value

  const firstDay = new Date(year, month, 1)
  const lastDay = new Date(year, month + 1, 0)

  const weekStartsOnSunday = currentLanguage.value === 'en'

  let firstDayOfWeek = firstDay.getDay()

  if (!weekStartsOnSunday) {
    firstDayOfWeek = firstDayOfWeek || 7
  }

  const daysInPrevMonth = new Date(year, month, 0).getDate()

  const days = []

  const daysToAdd = weekStartsOnSunday ? firstDayOfWeek : firstDayOfWeek - 1
  for (let i = daysToAdd; i > 0; i--) {
    const dayNumber = daysInPrevMonth - i + 1
    days.push({
      dayNumber,
      isCurrentMonth: false,
      isToday: false,
      isSelected: false,
      key: `prev-${dayNumber}`,
      date: new Date(year, month - 1, dayNumber)
    })
  }

  for (let dayNumber = 1; dayNumber <= lastDay.getDate(); dayNumber++) {
    const date = new Date(year, month, dayNumber)
    const today = new Date()
    const isToday = date.toDateString() === today.toDateString()
    const isSelected = selectedDate.value && date.toDateString() === selectedDate.value.toDateString()

    days.push({
      dayNumber,
      isCurrentMonth: true,
      isToday,
      isSelected,
      key: `current-${dayNumber}`,
      date
    })
  }

  const remainingDays = 42 - days.length;
  for (let dayNumber = 1; dayNumber <= remainingDays; dayNumber++) {
    days.push({
      dayNumber,
      isCurrentMonth: false,
      isToday: false,
      isSelected: false,
      key: `next-${dayNumber}`,
      date: new Date(year, month + 1, dayNumber)
    })
  }

  return days;
});

function initCalendar() {
  if (props.initialDate) {
    const [year, month, day] = props.initialDate.split('-').map(Number)
    currentDate.value = new Date(year, month - 1, day)
    selectedDate.value = new Date(year, month - 1, day)
  } else {
    const today = new Date()
    currentDate.value = new Date(today.getFullYear(), today.getMonth(), 1)
    selectedDate.value = today
  }
}

onMounted(() => {
  initCalendar();
});

function changeToPreviousMonth() {
  currentDate.value = new Date(currentYear.value, currentMonth.value - 1, 1)
}

function changeToNextMonth() {
  currentDate.value = new Date(currentYear.value, currentMonth.value + 1, 1)
}

</script>
<template>
  <!-- компонент календаря - {{ currentDate }}</br>
  язык - {{ currentLanguage }}</br>
  currentYear - {{ currentYear }}</br>
  currentMonth - {{ currentMonth }}</br>
  currentMonthName - {{ currentMonthName }}</br>
  weekDays - {{ weekDays }}</br>
  calendarDays - {{ calendarDays }}</br> -->
  <div class="calendar-container">
    <div class="calendar-header">
      <button @click="changeToPreviousMonth" class="nav-btn">
        &#9668;
      </button>
      <p class="month-year">
        {{ currentMonthName }} {{ currentYear }}
      </p>
      <button @click="changeToNextMonth" class="nav-btn">
        &#9658;
      </button>
    </div>
  </div>
  
</template>

<style scoped>
.calendar-container {
  width: 320px;
  box-sizing: border-box;
  border: 1px solid #808080;
  border-radius: 12px;
}
.calendar-header {
  display: flex;
  justify-content: space-between;
  padding: 8px;
}
.month-year {
  font-size: 18px;
  padding: 0;
  margin: 0;
}
.nav-btn {
  width: 24px;
  height: 24px;
  border: none;
  background: none;
  cursor: pointer;
}
</style>