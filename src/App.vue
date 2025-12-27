<template>
  <main class="page" :dir="direction">
    <header class="hero">
      <div>
        <p class="eyebrow">{{ t("eyebrow") }}</p>
        <h1>{{ t("title") }}</h1>
        <p class="subhead">{{ t("subtitle") }}</p>
      </div>
      <div class="controls">
        <label class="control-label" for="language-select">
          {{ t("languageLabel") }}
        </label>
        <select
          id="language-select"
          v-model="language"
          class="control-select"
          aria-label="Language"
        >
          <option value="en">English</option>
          <option value="fr">Français</option>
          <option value="ar">العربية</option>
        </select>
      </div>
      <div class="legend">
        <span v-for="item in legend" :key="item.key" class="legend-item">
          <span class="legend-dot" :style="{ backgroundColor: item.color }"></span>
          {{ t(item.key) }}
        </span>
      </div>
    </header>

    <section class="card">
      <Line :data="chartData" :options="chartOptions" />
    </section>
  </main>
</template>

<script setup>
import { computed, ref } from "vue";
import {
  Chart as ChartJS,
  CategoryScale,
  LinearScale,
  PointElement,
  LineElement,
  Tooltip,
  Legend,
} from "chart.js";
import { Line } from "vue-chartjs";
import prayerTimes from "../agadir_prayer_times_2025.json";

ChartJS.register(
  CategoryScale,
  LinearScale,
  PointElement,
  LineElement,
  Tooltip,
  Legend,
);

const prayerData = prayerTimes
  .slice()
  .sort((a, b) => new Date(a.Date) - new Date(b.Date));

const prayerSeries = [
  { key: "Fajr", color: "#1b5e20" },
  { key: "Sunrise (Shuruq)", color: "#f9a825" },
  { key: "Dhuhr", color: "#1565c0" },
  { key: "Asr", color: "#6a1b9a" },
  { key: "Maghrib", color: "#ef6c00" },
  { key: "Isha", color: "#283593" },
];

const language = ref("en");

const translations = {
  en: {
    eyebrow: "Prayer Times",
    title: "Daily Prayer Schedule Across the Year",
    subtitle:
      "Each line shows the daily time (in hours) for its prayer across the calendar.",
    languageLabel: "Language",
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

const direction = computed(() => (language.value === "ar" ? "rtl" : "ltr"));

const timeToHours = (timeValue) => {
  if (!timeValue || typeof timeValue !== "string") {
    return null;
  }
  const [hours, minutes] = timeValue.split(":").map(Number);
  return Number.isNaN(hours) || Number.isNaN(minutes)
    ? null
    : hours + minutes / 60;
};

const localeMap = {
  en: "en-US",
  fr: "fr-FR",
  ar: "ar-MA",
};

const labels = computed(() =>
  prayerData.map((entry) =>
    new Date(entry.Date).toLocaleDateString(localeMap[language.value], {
      month: "short",
      day: "numeric",
    }),
  ),
);

const chartData = computed(() => ({
  labels: labels.value,
  datasets: prayerSeries.map((prayer) => ({
    label: t(prayer.key),
    data: prayerData.map((entry) => timeToHours(entry[prayer.key])),
    borderColor: prayer.color,
    backgroundColor: prayer.color,
    tension: 0.3,
    pointRadius: 3,
    pointHoverRadius: 5,
  })),
}));

const chartOptions = computed(() => ({
  responsive: true,
  maintainAspectRatio: false,
  interaction: {
    intersect: false,
    mode: "index",
  },
  scales: {
    y: {
      title: {
        display: true,
        text: t("yAxis"),
      },
      ticks: {
        callback: (value) => `${value}:00`,
      },
    },
    x: {
      title: {
        display: true,
        text: t("xAxis"),
      },
    },
  },
  plugins: {
    legend: {
      display: false,
    },
    tooltip: {
      padding: 16,
      boxPadding: 8,
      bodySpacing: 6,
      titleSpacing: 8,
      titleMarginBottom: 8,
      bodyFont: {
        size: 14,
        weight: "600",
      },
      titleFont: {
        size: 14,
        weight: "700",
      },
      footerFont: {
        size: 12,
        weight: "600",
      },
      callbacks: {
        label: (context) => {
          const hours = Math.floor(context.parsed.y);
          const minutes = Math.round((context.parsed.y - hours) * 60)
            .toString()
            .padStart(2, "0");
          return `${context.dataset.label}\u2003\u2003${hours}:${minutes}`;
        },
      },
    },
  },
}));

const legend = prayerSeries;
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

.hero {
  display: flex;
  align-items: flex-start;
  justify-content: space-between;
  gap: 24px;
  flex-wrap: wrap;
}

.controls {
  display: flex;
  flex-direction: column;
  gap: 8px;
  background: #fff;
  padding: 12px 16px;
  border-radius: 14px;
  box-shadow: 0 10px 30px rgba(15, 23, 42, 0.08);
}

.control-label {
  font-size: 0.8rem;
  font-weight: 600;
  color: #334155;
}

.control-select {
  border-radius: 10px;
  border: 1px solid #cbd5f5;
  padding: 8px 10px;
  font-size: 0.95rem;
  background: #f8fafc;
  color: #1f2933;
}

.eyebrow {
  text-transform: uppercase;
  letter-spacing: 0.18em;
  font-size: 0.75rem;
  font-weight: 600;
  color: #3f51b5;
  margin: 0 0 8px;
}

h1 {
  font-size: clamp(2rem, 3vw, 2.6rem);
  margin: 0 0 12px;
}

.subhead {
  margin: 0;
  max-width: 500px;
  color: #4b5563;
  line-height: 1.6;
}

.legend {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(140px, 1fr));
  gap: 12px;
  background: #fff;
  padding: 16px 20px;
  border-radius: 16px;
  box-shadow: 0 10px 30px rgba(15, 23, 42, 0.08);
}

.legend-item {
  display: flex;
  align-items: center;
  gap: 8px;
  font-size: 0.9rem;
  color: #1f2933;
}

.legend-dot {
  width: 10px;
  height: 10px;
  border-radius: 999px;
}

.card {
  background: #fff;
  border-radius: 24px;
  padding: 24px;
  min-height: 480px;
  box-shadow: 0 18px 45px rgba(15, 23, 42, 0.12);
}

@media (max-width: 720px) {
  .page {
    padding: 32px 16px 48px;
  }

  .card {
    padding: 16px;
  }
}
</style>
