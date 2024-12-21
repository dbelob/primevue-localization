<script setup lang="ts">
import DatePicker from 'primevue/datepicker';
import Select from 'primevue/select';
import { onMounted, ref, watchEffect } from 'vue';
import { defaultOptions, usePrimeVue } from 'primevue/config';

const primevue = usePrimeVue();

const browserLocale = navigator.languages?.[0] || navigator.language;
const osLocale = new Intl.DateTimeFormat().resolvedOptions().locale;

let primeVueLocaleFiles = [];
const selectedLocale = ref();
const localeOptions = ref([]);
const date = ref(new Date(2023, 10, 28));

onMounted(() => {
  // Not sure why a relative path is needed here, "@/" or "./" doesn't work
  primeVueLocaleFiles = import.meta.glob("../node_modules/primelocale/*.json");

  localeOptions.value = Object.keys(primeVueLocaleFiles)
      .map((e) => e.match(/\/([^/]+)\.json$/)[1])
      .filter((e) => e.split("-")[0].length === 2);
});

watchEffect(() => updatePrimeVueConfigWithLocaleTag(selectedLocale.value));

async function updatePrimeVueConfigWithLocaleTag(localeTag) {
  if (!localeTag) return;

  const localeRe = new RegExp(`\\b${localeTag}.json$`, "i");
  const langRe = new RegExp(`\\b${localeTag.split("-")[0]}.json$`, "i");
  let localeFileName = Object.keys(primeVueLocaleFiles).find((locale) =>
      localeRe.test(locale)
  );
  localeFileName ??= Object.keys(primeVueLocaleFiles).find((locale) =>
      langRe.test(locale)
  );

  const localeFile =
      Object.values(
          (await primeVueLocaleFiles[localeFileName]?.())?.default
      )[0] ?? defaultOptions.locale;

  primevue.config.locale = {
    ...primevue.config.locale,
    ...localeFile,
  };

  // This is a hack to force PrimeVue to re-render the calendar
  date.value = new Date(2023, 10, 28);
}
</script>

<template>
  <div>
    <h2>PrimeVue Locale Switcher</h2>
    <div>Browser locale: {{ browserLocale }}</div>
    <div>OS locale: {{ osLocale }}</div>
    <div>Selected locale: {{ selectedLocale }}</div>

    <div style="width: 200px">
      <Select
          v-model="selectedLocale"
          :options="localeOptions"
          placeholder="Select a locale"
          style="width: 100%"
      />
    </div>

    <div style="width: 200px">
      <DatePicker v-model="date"/>
    </div>
  </div>
</template>
