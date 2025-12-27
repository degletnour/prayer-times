<template>
  <I18nProvider v-slot="{ direction }">
    <main class="page" :dir="direction">
      <LayoutHeader
        v-model:city="city"
        :series="prayerSeries"
        :cities="cities"
      />
      <PrayerChart :series="prayerSeries" :city="city" />
    </main>
  </I18nProvider>
</template>

<script setup>
import { ref } from "vue";
import I18nProvider from "./components/I18nProvider.vue";
import LayoutHeader from "./components/LayoutHeader.vue";
import PrayerChart from "./components/PrayerChart.vue";

const city = ref("agadir");

const cities = [
  { id: "agadir", labelKey: "cityAgadir" },
  { id: "paris", labelKey: "cityParis" },
];

const prayerSeries = [
  { key: "Fajr", color: "#1b5e20" },
  { key: "Sunrise (Shuruq)", color: "#f9a825" },
  { key: "Dhuhr", color: "#1565c0" },
  { key: "Asr", color: "#6a1b9a" },
  { key: "Maghrib", color: "#ef6c00" },
  { key: "Isha", color: "#283593" },
];
</script>

<style scoped>
:global(body) {
  margin: 0;
  font-family: "Inter", "Segoe UI", system-ui, sans-serif;
  color: #1f2933;
  background: #f5f7fb;
}

.page {
  min-height: 100vh;
  padding: 48px 24px 64px;
  max-width: 1100px;
  margin: 0 auto;
  display: flex;
  flex-direction: column;
  gap: 32px;
}

@media (max-width: 720px) {
  .page {
    padding: 32px 16px 48px;
  }
}
</style>
