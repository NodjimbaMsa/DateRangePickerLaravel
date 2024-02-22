<template>
  <div class="connected-calendars">
    <PrimaryButton @click="toggleVisibility">Filtre des évènements</PrimaryButton>
    <div class="calendars" v-if="areCalendarsVisible">
      <Calendar selectedDates="selectedDates"/>
      <Calendar selectedDates="selectedDates"/>
    </div>
  </div>
</template>

<script setup>
import { ref, provide } from 'vue';
import Calendar from './Calendar.vue';
import PrimaryButton from './PrimaryButton.vue';

const areCalendarsVisible = ref(false);
const selectedDates = ref([]);

const toggleVisibility = () => {
  areCalendarsVisible.value = !areCalendarsVisible.value;
};

provide('selectedDates', selectedDates);

</script>


<style scoped>
.connected-calendars {
  display: flex;
  flex-direction: column;
  align-items: center;
}

.calendars {
  display: flex;
}
</style>
