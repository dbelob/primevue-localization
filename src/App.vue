<script setup lang="ts">
import { onMounted, ref } from 'vue';
import DatePicker from 'primevue/datepicker';
import { usePrimeVue } from 'primevue/config';
import localeRu from 'primelocale/ru.json';

const primevue = usePrimeVue();
const date = ref(new Date(2023, 10, 28));

onMounted(() => {
  primevue.config.locale = {
    ...primevue.config.locale,
    ...localeRu['ru'],
  };
});
</script>

<template>
  <div>
    <h2>PrimeVue With One Language</h2>
    <div style="width: 200px">
      <DatePicker v-model="date"/>
    </div>
  </div>
</template>
