<script setup>
import { ref, computed, onMounted } from 'vue';

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

const emit = defineEmits(['language-changed', 'date-selected'])

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

function changeLanguage() {
  emit('language-changed', currentLanguage.value)
}

function selectDate(day) {
  if (day.isCurrentMonth) {
    selectedDate.value = day.date
    emit('date-selected', {
      date: day.date,
      formattedDate: formatDate(day.date)
    })
  }
}

function formatDate(date) {
  const year = date.getFullYear()
  const month = String(date.getMonth() + 1).padStart(2, '0')
  const day = String(date.getDate()).padStart(2, '0')
  return `${year}-${month}-${day}`
}

</script>
<template>
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
    <div class="weekdays">
      <div
        v-for="day in weekDays"
        :key="day"
        class="weekday"
      >
        {{ day }}
      </div>
    </div>
    <div class="days">
      <div
        v-for="day in calendarDays"
        :key="day.key"
        :class="[
          'day',
          {
            'other-month': !day.isCurrentMonth,
            'today': day.isToday,
            'selected': day.isSelected,
            'clickable': day.isCurrentMonth
          }
        ]"
        @click="selectDate(day)"
      >
        {{ day.dayNumber }}
      </div>
    </div>
    <div class="language-selector">
      <label for="language">
        {{ currentLanguage === 'ru' ? 'Язык:' : 'Language' }}
      </label>
      <select id="language" v-model="currentLanguage" @change="changeLanguage">
        <option value="ru">
          Русский
        </option>
        <option value="en">
          English
        </option>
      </select>
    </div>
  </div>
  
</template>

<style scoped>
.calendar-container {
  width: 320px;
  box-sizing: border-box;
  border: 1px solid #808080;
  border-radius: 12px;
  overflow: hidden;
}

.calendar-header {
  display: flex;
  justify-content: space-between;
  padding: 8px;
  border-bottom: 1px solid #808080;
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

.weekdays {
  display: grid;
  grid-template-columns: repeat(7, 1fr);
  border-bottom: 1px solid #808080;
}
.weekday {
  padding: 12px 8px;
  text-align: center;
  font-size: 14px;
  font-weight: 600;
}

.days {
  display: grid;
  grid-template-columns: repeat(7, 1fr);
  border-bottom: 1px solid #808080;
}

.day {
  padding: 12px 8px;
  text-align: center;
  cursor: default;
  position: relative;
}

.day.other-month {
  color: #ccc;
  background-color: #fff;
}
.day.clickable {
  cursor: pointer;
}
.day.clickable:hover {
  background-color: #808080;
}
.day.today {
  background-color: #808080;
  color: white;
}
.day.selected {
  background-color: #000;
  color: white;
}

.language-selector {
  padding: 15px;
  display: flex;
  align-items: center;
  gap: 10px;
}
.language-selector select {
  padding: 8px 12px;
  border: 1px solid #ddd;
  border-radius: 4px;
  background-color: white;
  cursor: pointer;
}
.language-selector select:focus {
  outline: none;
  border-color: #808080;
}
</style>