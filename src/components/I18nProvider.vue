<template>
  <slot :direction="direction" />
</template>

<script setup>
import { computed, provide, ref } from "vue";

const language = ref("en");

const translations = {
  en: {
    eyebrow: "Prayer Times",
    title: "Daily Prayer Schedule Across the Year",
    subtitle:
      "Each line shows the daily time (in hours) for its prayer across the calendar.",
    languageLabel: "Language",
    cityLabel: "City",
    cityAgadir: "Agadir",
    cityParis: "Paris",
    Fajr: "Fajr",
    "Sunrise (Shuruq)": "Sunrise",
    Dhuhr: "Dhuhr",
    Asr: "Asr",
    Maghrib: "Maghrib",
    Isha: "Isha",
    yAxis: "Hour of Day",
    xAxis: "Date",
  },
  fr: {
    eyebrow: "Heures de prière",
    title: "Calendrier quotidien des prières sur l'année",
    subtitle:
      "Chaque ligne indique l'heure quotidienne (en heures) de la prière.",
    languageLabel: "Langue",
    cityLabel: "Ville",
    cityAgadir: "Agadir",
    cityParis: "Paris",
    Fajr: "Fajr",
    "Sunrise (Shuruq)": "Lever du soleil",
    Dhuhr: "Dhohr",
    Asr: "Asr",
    Maghrib: "Maghrib",
    Isha: "Icha",
    yAxis: "Heure de la journée",
    xAxis: "Date",
  },
  ar: {
    eyebrow: "مواقيت الصلاة",
    title: "جدول الصلوات اليومي على مدار السنة",
    subtitle: "يعرض كل خط وقت الصلاة اليومي (بالساعات) عبر التقويم.",
    languageLabel: "اللغة",
    cityLabel: "المدينة",
    cityAgadir: "أكادير",
    cityParis: "باريس",
    Fajr: "الفجر",
    "Sunrise (Shuruq)": "الشروق",
    Dhuhr: "الظهر",
    Asr: "العصر",
    Maghrib: "المغرب",
    Isha: "العشاء",
    yAxis: "الساعة في اليوم",
    xAxis: "التاريخ",
  },
};

const t = (key) => translations[language.value]?.[key] ?? key;

const localeMap = {
  en: "en-US",
  fr: "fr-FR",
  ar: "ar-MA",
};

const direction = computed(() => (language.value === "ar" ? "rtl" : "ltr"));
const locale = computed(() => localeMap[language.value]);

provide("i18n", {
  language,
  t,
  locale,
  direction,
});
</script>
