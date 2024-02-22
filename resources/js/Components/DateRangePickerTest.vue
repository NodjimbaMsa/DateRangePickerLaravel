<template>
  <div class="calendar">
    <button @click="toggleVisibility">Toggle Calendar</button>
    <div v-if="isCalendarVisible">
      <div class="calendar-header">
        <button @click="previousMonth">&lt;</button>
        <select v-model="selectedMonth" @change="updateCalendar">
          <option v-for="(month, index) in months" :key="index" :value="index">{{ month }}</option>
        </select>
        <select v-model="selectedYear" @change="updateCalendar">
          <option v-for="year in years" :key="year">{{ year }}</option>
        </select>
        <button @click="nextMonth">&gt;</button>
      </div>
      <div class="calendar-grid">
        <div class="day" v-for="day in days" :key="day">{{ day }}</div>
        <div
          class="date"
          v-for="date in dates"
          :key="date"
          :class="{ 'selected': isDateSelected(date), 'highlighted': isDateHighlighted(date, selectedMonth, selectedYear) }"
          @click="selectDate(date)"
        >
          <span v-if="date">{{ date }}</span>
          <span v-else>&nbsp;</span>
        </div>
      </div>
    </div>
    <div class="calendar-footer" v-if="isCalendarVisible">
      <div v-if="selectedDates.length === 2">
        <button @click="clearDates">Clear Dates</button>
        <div>Selected Dates:</div>
        <div>{{ formatDate(selectedDates[0]) }} - {{ formatDate(selectedDates[1]) }}</div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, computed } from 'vue';

const currentDate = new Date();
const selectedYear = ref(currentDate.getFullYear());
const selectedMonth = ref(currentDate.getMonth());
const selectedDates = ref([]);
const isCalendarVisible = ref(true);

const months = [
  "Janvier", "Février", "Mars", "Avril", "Mai", "Juin",
  "Juillet", "Aout", "Septembre", "Octobre", "Novembre", "Décembre"
];

const days = ["Dim", "Lun", "Mar", "Mer", "Jeu", "Ven", "Sam"];

const dates = computed(() => {
  const firstDayOfMonth = new Date(selectedYear.value, selectedMonth.value, 1).getDay();
  const lastDateOfMonth = new Date(selectedYear.value, selectedMonth.value + 1, 0).getDate();
  const datesArray = [];

  for (let i = 1; i <= lastDateOfMonth; i++) {
    datesArray.push(i);
  }

  for (let i = 0; i < firstDayOfMonth; i++) {
    datesArray.unshift('');
  }

  return datesArray;
});

const years = computed(() => {
  const currentYear = new Date().getFullYear();
  return Array.from({ length: 10 }, (_, i) => currentYear + i);
});

const previousMonth = () => {
  if (selectedMonth.value === 0) {
    selectedMonth.value = 11;
    selectedYear.value--;
  } else {
    selectedMonth.value--;
  }
};

const nextMonth = () => {
  if (selectedMonth.value === 11) {
    selectedMonth.value = 0;
    selectedYear.value++;
  } else {
    selectedMonth.value++;
  }
};

const updateCalendar = () => {
  const selectedDate = new Date(selectedYear.value, selectedMonth.value, 1);
  const newYear = selectedDate.getFullYear();
  const newMonth = selectedDate.getMonth();

  selectedYear.value = newYear;
  selectedMonth.value = newMonth;
};

const selectDate = (date) => {
  if (date !== '') {
    const selectedDate = new Date(selectedYear.value, selectedMonth.value, date);
    if (selectedDates.value.length === 0) {
      selectedDates.value.push(selectedDate);
    } else if (selectedDates.value.length === 1) {
      const existingDate = selectedDates.value[0];
      if (selectedDate < existingDate) {
        selectedDates.value.unshift(selectedDate);
      } else {
        selectedDates.value.push(selectedDate);
      }
    } else {
      selectedDates.value = [selectedDate];
    }
  }
};

const clearDates = () => {
  selectedDates.value = [];
};

const formatDate = (date) => {
  return `${date.getDate()}/${date.getMonth() + 1}/${date.getFullYear()}`;
};

const toggleVisibility = () => {
  isCalendarVisible.value = !isCalendarVisible.value;
};

const isDateSelected = (date) => {
  return selectedDates.value.some(selectedDate => selectedDate.getDate() === date);
};

const isDateHighlighted = (date, month, year) => {
  if (selectedDates.value.length === 2) {
    const [startDate, endDate] = selectedDates.value;
    const startMonth = startDate.getMonth();
    const startYear = startDate.getFullYear();
    const endMonth = endDate.getMonth();
    const endYear = endDate.getFullYear();

    if (
      (year > startYear || (year === startYear && month > startMonth)) &&
      (year < endYear || (year === endYear && month < endMonth))
    ) {
      return true;
    } else if (
      year === startYear && year === endYear &&
      month === startMonth && month === endMonth
    ) {
      return date > startDate.getDate() && date < endDate.getDate();
    } else if (year === startYear && month === startMonth) {
      return date > startDate.getDate();
    } else if (year === endYear && month === endMonth) {
      return date < endDate.getDate();
    }
  }
  return false;
};

</script>

<style scoped>
.calendar {
  width: 400px;
  border: 1px solid #ccc;
  border-radius: 5px;
  padding: 10px;
}

.calendar-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 10px;
}

.calendar-grid {
  display: grid;
  grid-template-columns: repeat(7, 1fr);
  gap: 5px;
}

.day, .date {
  text-align: center;
  padding: 5px;
  border-radius: 3px;
  cursor: pointer;
}

.day {
  background-color: #f0f0f0;
}

.date:hover {
  background-color: #e0e0e0;
}

.selected {
  background-color: #ccc; /* Color for selected dates */
}

.highlighted {
  background-color: lightgreen; /* Color for dates between start and end dates */
}

.calendar-footer {
  margin-top: 10px;
}

.calendar-footer select {
  padding: 5px;
  font-size: 16px;
}

.calendar-button {
  background-color: transparent;
  border: none;
  padding: 0;
  font: inherit;
  cursor: pointer;
}
</style>
