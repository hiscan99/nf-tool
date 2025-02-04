<template>
  <div>
    <v-container class="fill-height">
      <v-row align="center" justify="center" style="height: 800px">
        <v-col>
          <v-card class="mx-auto" max-width="400">
            <v-card-title>Time Since Last</v-card-title>
            <v-card-subtitle>{{
              selectedDate && selectedTime
                ? timeSinceEntered
                : "Please add data using show fields"
            }}</v-card-subtitle>
            <v-card-text>
              <div v-if="showFields">
                <v-text-field
                  v-model="selectedDate"
                  label="Enter Date"
                  type="date"
                >
                </v-text-field>
                <v-text-field
                  v-model="selectedTime"
                  label="Enter time"
                  type="time"
                >
                </v-text-field>
              </div>
            </v-card-text>

            <v-card-actions>
              <v-btn color="secondary" variant="text" @click="toggleFields"
                >Show fields</v-btn
              >
              <v-btn
                color="secondary"
                variant="text"
                @click="exportDateTimeLocal"
                >Export data</v-btn
              >
            </v-card-actions>
          </v-card>
        </v-col>
      </v-row>
    </v-container>
  </div>
</template>

<script setup>
import { ref, onMounted, onUnmounted } from "vue";

// Added reactive variables for date and time
const selectedDate = ref(null);
const selectedTime = ref(null);
const currentTime = ref(new Date());
const showFields = ref(false);

const saveDateTimeToLocalStorage = () => {
  localStorage.setItem("selectedDate", selectedDate.value);
  localStorage.setItem("selectedTime", selectedTime.value);
};

watch(selectedDate, saveDateTimeToLocalStorage);
watch(selectedTime, saveDateTimeToLocalStorage);

const toggleFields = () => {
  showFields.value = !showFields.value;
};

// Function to export date and time to a text file
const exportDateTimeLocal = () => {
  const dateTime = `Date: ${selectedDate.value}\nTime: ${selectedTime.value}`;
  const blob = new Blob([dateTime], { type: "text/plain" });
  const link = document.createElement("a");
  link.href = URL.createObjectURL(blob);
  link.download = "dateTime.txt";
  link.click();
}; // Added reactive variables for date and time

const timeSinceEntered = computed(() => {
  if (!selectedDate.value || !selectedTime.value) return 0;
  const enteredDateTime = new Date(
    `${selectedDate.value}T${selectedTime.value}`
  );
  const diffTime = Math.abs(currentTime.value - enteredDateTime);

  const days = Math.floor(diffTime / (1000 * 60 * 60 * 24));
  const hours = Math.floor(
    (diffTime % (1000 * 60 * 60 * 24)) / (1000 * 60 * 60)
  );
  const minutes = Math.floor((diffTime % (1000 * 60 * 60)) / (1000 * 60));
  const seconds = Math.floor((diffTime % (1000 * 60)) / 1000);

  return `${days} days, ${hours} hours, ${minutes} minutes, ${seconds} seconds`;
});

let intervalId;
onMounted(() => {
  intervalId = setInterval(() => {
    currentTime.value = new Date();
  }, 1000);

  // Load date and time from localStorage
  selectedDate.value = localStorage.getItem("selectedDate") || null;
  selectedTime.value = localStorage.getItem("selectedTime") || null;
});

onUnmounted(() => {
  clearInterval(intervalId);
});
</script>
