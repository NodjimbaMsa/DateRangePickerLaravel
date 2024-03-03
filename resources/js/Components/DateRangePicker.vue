<template>
  <div class="connected-calendars">
    <PrimaryButton @click="toggleVisibility">Filtre des évènements</PrimaryButton>
    <div class="calendars" v-if="areCalendarsVisible">
      <Calendar v-model:selectedDates="selectedDates" @update:selectedDates="updateDates"/>
      <Calendar v-model:selectedDates="selectedDates" @update:selectedDates="updateDates"/>
    </div>
  </div>
</template>

<script setup>
import { ref, provide, defineProps, defineEmits } from 'vue';
import Calendar from './Calendar.vue';
import PrimaryButton from './PrimaryButton.vue';

const props = defineProps({
  selectedDates: {
    type: Array,
    default: () => [],
  },
});

const emits = defineEmits(['update:selectedDates']);

const areCalendarsVisible = ref(false);
const selectedDates = ref([]);

const toggleVisibility = () => {
  areCalendarsVisible.value = !areCalendarsVisible.value;
};

const updateDates = (newDates) => {
  emits('update:selectedDates', newDates);
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
