<template>
  <main class="page">
    <header class="hero">
      <div>
        <p class="eyebrow">Prayer Times</p>
        <h1>Daily Prayer Schedule Across the Year</h1>
        <p class="subhead">
          Each line shows the daily time (in hours) for its prayer across the
          calendar.
        </p>
      </div>
      <div class="legend">
        <span v-for="item in legend" :key="item.label" class="legend-item">
          <span class="legend-dot" :style="{ backgroundColor: item.color }"></span>
          {{ item.label }}
        </span>
      </div>
    </header>

    <section class="card">
      <Line :data="chartData" :options="chartOptions" />
    </section>
  </main>
</template>

<script setup>
import { computed } from "vue";
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

ChartJS.register(
  CategoryScale,
  LinearScale,
  PointElement,
  LineElement,
  Tooltip,
  Legend,
);

const prayerData = [
  {
    Date: "2025-01-01",
    Day: "Wednesday",
    Fajr: "07:04",
    "Sunrise (Shuruq)": "08:35",
    Dhuha: "09:05",
    Dhuhr: "13:45",
    Asr: "16:31",
    Maghrib: "18:50",
    Isha: "20:13",
    "Qibla Hour": NaN,
  },
  {
    Date: "2025-02-01",
    Day: "Saturday",
    Fajr: "06:45",
    "Sunrise (Shuruq)": "08:10",
    Dhuha: "08:40",
    Dhuhr: "13:40",
    Asr: "16:45",
    Maghrib: "19:10",
    Isha: "20:30",
    "Qibla Hour": NaN,
  },
  {
    Date: "2025-03-01",
    Day: "Saturday",
    Fajr: "06:10",
    "Sunrise (Shuruq)": "07:35",
    Dhuha: "08:05",
    Dhuhr: "13:30",
    Asr: "16:50",
    Maghrib: "19:35",
    Isha: "20:55",
    "Qibla Hour": NaN,
  },
  {
    Date: "2025-04-01",
    Day: "Tuesday",
    Fajr: "05:30",
    "Sunrise (Shuruq)": "06:55",
    Dhuha: "07:25",
    Dhuhr: "13:20",
    Asr: "16:55",
    Maghrib: "20:00",
    Isha: "21:20",
    "Qibla Hour": NaN,
  },
  {
    Date: "2025-05-01",
    Day: "Thursday",
    Fajr: "04:55",
    "Sunrise (Shuruq)": "06:15",
    Dhuha: "06:45",
    Dhuhr: "13:15",
    Asr: "17:05",
    Maghrib: "20:20",
    Isha: "21:40",
    "Qibla Hour": NaN,
  },
  {
    Date: "2025-06-01",
    Day: "Sunday",
    Fajr: "04:30",
    "Sunrise (Shuruq)": "05:55",
    Dhuha: "06:25",
    Dhuhr: "13:20",
    Asr: "17:20",
    Maghrib: "20:40",
    Isha: "22:00",
    "Qibla Hour": NaN,
  },
];

const prayerSeries = [
  { key: "Fajr", label: "Fajr", color: "#1b5e20" },
  { key: "Sunrise (Shuruq)", label: "Sunrise", color: "#f9a825" },
  { key: "Dhuhr", label: "Dhuhr", color: "#1565c0" },
  { key: "Asr", label: "Asr", color: "#6a1b9a" },
  { key: "Maghrib", label: "Maghrib", color: "#ef6c00" },
  { key: "Isha", label: "Isha", color: "#283593" },
];

const timeToHours = (timeValue) => {
  if (!timeValue || typeof timeValue !== "string") {
    return null;
  }
  const [hours, minutes] = timeValue.split(":").map(Number);
  return Number.isNaN(hours) || Number.isNaN(minutes)
    ? null
    : hours + minutes / 60;
};

const labels = computed(() =>
  prayerData.map((entry) =>
    new Date(entry.Date).toLocaleDateString("en-US", {
      month: "short",
      day: "numeric",
    }),
  ),
);

const chartData = computed(() => ({
  labels: labels.value,
  datasets: prayerSeries.map((prayer) => ({
    label: prayer.label,
    data: prayerData.map((entry) => timeToHours(entry[prayer.key])),
    borderColor: prayer.color,
    backgroundColor: prayer.color,
    tension: 0.3,
    pointRadius: 3,
    pointHoverRadius: 5,
  })),
}));

const chartOptions = {
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
        text: "Hour of Day",
      },
      ticks: {
        callback: (value) => `${value}:00`,
      },
    },
    x: {
      title: {
        display: true,
        text: "Date",
      },
    },
  },
  plugins: {
    legend: {
      display: false,
    },
    tooltip: {
      callbacks: {
        label: (context) => {
          const hours = Math.floor(context.parsed.y);
          const minutes = Math.round((context.parsed.y - hours) * 60)
            .toString()
            .padStart(2, "0");
          return `${context.dataset.label}: ${hours}:${minutes}`;
        },
      },
    },
  },
};

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
