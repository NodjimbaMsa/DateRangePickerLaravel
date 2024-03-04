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

// Variable pour la visibilité des calendriers
const areCalendarsVisible = ref(false);
const selectedDates = ref([]);

const toggleVisibility = () => {
  areCalendarsVisible.value = !areCalendarsVisible.value;
};

// Émission d'un événement pour mettre à jour les dates sélectionnées
const emits = defineEmits(['update:selectedDates']);

/**
 * Méthode responsable de mettre à jour les jours sélectionnés.
 * @param {Array} newDates - Nouvelles dates sélectionnées.
 */
 const updateDates = (newDates) => {
  emits('update:selectedDates', newDates);
};

// Fournit les dates sélectionnées aux composants enfants
provide('selectedDates', selectedDates);

</script>

<template>
  <div class="connected-calendars">
    <PrimaryButton @click="toggleVisibility">Filtre des évènements</PrimaryButton>
    <div class="calendars" v-if="areCalendarsVisible">
      <Calendar v-model:selectedDates="selectedDates" @update:selectedDates="updateDates"/>
      <Calendar v-model:selectedDates="selectedDates" @update:selectedDates="updateDates"/>
    </div>
  </div>
</template>


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
