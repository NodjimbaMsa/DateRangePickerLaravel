<script setup>
import { ref, computed, watch, inject, defineProps, defineEmits } from 'vue';

// Définition des propriétés et événements du composant
const props = defineProps({
    selectedDates: {
        type: Array,
        default: () => [],
    },
});

const emits = defineEmits(['update:selectedDates']);

// Initialisation des variables de date
const currentDate = new Date();
const selectedYear = ref(currentDate.getFullYear());
const selectedMonth = ref(currentDate.getMonth());
const selectedDates = inject('selectedDates', ref([])); // Injection des dates sélectionnées dans le composant parent

// Tableaux de mois et de jours
const months = [
    "Janvier", "Février", "Mars", "Avril", "Mai", "Juin",
    "Juillet", "Aout", "Septembre", "Octobre", "Novembre", "Décembre"
];

const days = ["Dim", "Lun", "Mar", "Mer", "Jeu", "Ven", "Sam"];

// Calcul des dates pour le mois actuel
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

// Calcul des années pour les options de sélection
const years = computed(() => {
    const currentYear = new Date().getFullYear();
    return Array.from({ length: 10 }, (_, i) => currentYear + i);
});

// Fonction pour passer au mois précédent
const previousMonth = () => {
    if (selectedMonth.value === 0) {
        selectedMonth.value = 11;
        selectedYear.value--;
    } else {
        selectedMonth.value--;
    }
};

// Fonction pour passer au mois suivant
const nextMonth = () => {
    if (selectedMonth.value === 11) {
        selectedMonth.value = 0;
        selectedYear.value++;
    } else {
        selectedMonth.value++;
    }
};

// Fonction de mise à jour du calendrier
const updateCalendar = () => {
    const selectedDate = new Date(selectedYear.value, selectedMonth.value, 1);
    const newYear = selectedDate.getFullYear();
    const newMonth = selectedDate.getMonth();

    selectedYear.value = newYear;
    selectedMonth.value = newMonth;
};

// Fonction pour sélectionner une date
const selectDate = (date) => {
    if (date !== '') { // Si la date n'est pas vide
        const selectedDate = new Date(selectedYear.value, selectedMonth.value, date); // Création de la date sélectionnée
        if (selectedDates.value.length === 0) { // Si aucune date n'a été sélectionnée ultérieurement
            selectedDates.value.push(selectedDate); // Ajouter la date à la liste des dates sélectionnées
        } else if (selectedDates.value.length === 1) { // Si une seule date est sélectionnée ultérieurement
            const existingDate = selectedDates.value[0];
            if (selectedDate < existingDate) { // Si la nouvelle date est antérieure à la date déjà sélectionnée
                selectedDates.value.unshift(selectedDate); // Insérer la nouvelle date au début de la liste
            } else {
                selectedDates.value.push(selectedDate); // Sinon, ajouter la nouvelle date à la fin de la liste
            }
        } else {
            selectedDates.value = [selectedDate]; // Si deux dates sont déjà sélectionnées, remplacer les dates sélectionnées par la nouvelle date
        }
        emits('update:selectedDates', selectedDates.value); // Émission de l'événement avec les dates sélectionnées
    }
};

// Fonction pour formater une date
const formatDate = (date) => {
    return `${date.getDate()}/${date.getMonth() + 1}/${date.getFullYear()}`;
};

// Fonction pour vérifier si une date est sélectionnée
const isDateSelected = (date) => {
    return selectedDates.value.some(selectedDate => selectedDate.getDate() === date);
};

// Fonction pour vérifier si une date esnt en surbrillance : est entre la date de début et la date de fin
const isDateHighlighted = (date, month, year) => {
    const currentDate = new Date(year, month, date);

    if (selectedDates.value.length === 2) {
        const [startDate, endDate] = selectedDates.value; // Récupère la date de début et de fin

        // Vérifier si la date actuelle est entre la date de début et la date de fin
        if (currentDate > startDate && currentDate < endDate) {
            return true;
        }
    }

    return false;
};

watch(selectedDates, (newSelectedDates, oldSelectedDates) => {
    // Logique pour mettre à jour le calendrier en réponse aux changements dans les dates sélectionnées
    //console.log('Anciennes dates sélectionnées :', oldSelectedDates);
    //console.log('Nouvelles dates sélectionnées :', newSelectedDates);
    // Mettre à jour le calendrier ou effectuer d'autres actions nécessaires
});


</script>


<template>
    <div class="calendar">
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
            <div class="date" v-for="date in dates" :key="date"
                :class="{ 'selected': isDateSelected(date), 'highlighted': isDateHighlighted(date, selectedMonth, selectedYear) }"
                @click="selectDate(date)">
                <span v-if="date">{{ date }}</span>
                <span v-else>&nbsp;</span>
            </div>
        </div>
        <div class="calendar-footer">
            <div v-if="selectedDates.length === 2">
                <div>Selected Dates:</div>
                <div>{{ formatDate(selectedDates[0]) }} - {{ formatDate(selectedDates[1]) }}</div>
            </div>
        </div>
    </div>
</template>

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

.day,
.date {
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
    background-color: #ccc;
    /* Couleur des dates sélectionnées */
}

.highlighted {
    background-color: lightgreen;
    /* Couleur des dates entre le début et la fin */
}

.calendar-footer {
    margin-top: 10px;
}

.calendar-footer select {
    padding: 5px;
    font-size: 16px;
}
</style>
