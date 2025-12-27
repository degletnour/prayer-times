<template>
  <section class="card">
    <Line :data="chartData" :options="chartOptions" />
  </section>
</template>

<script setup>
import { computed, inject, ref, watch } from "vue";
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

const { t, locale } = inject("i18n");

const props = defineProps({
  series: {
    type: Array,
    required: true,
  },
  city: {
    type: String,
    required: true,
  },
});

const cityFiles = {
  agadir: "/agadir_prayer_times_2025.json",
  paris: "/paris_prayer_times_2025.json",
};

const prayerData = ref([]);

const loadCityData = async () => {
  const filePath = cityFiles[props.city];
  if (!filePath) {
    prayerData.value = [];
    return;
  }

  try {
    const response = await fetch(filePath);
    if (!response.ok) {
      prayerData.value = [];
      return;
    }
    const data = await response.json();
    prayerData.value = Array.isArray(data)
      ? data.slice().sort((a, b) => new Date(a.Date) - new Date(b.Date))
      : [];
  } catch (error) {
    prayerData.value = [];
  }
};

watch(
  () => props.city,
  () => {
    loadCityData();
  },
  { immediate: true },
);

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
  prayerData.value.map((entry) =>
    new Date(entry.Date).toLocaleDateString(locale.value, {
      month: "short",
      day: "numeric",
    }),
  ),
);

const chartData = computed(() => ({
  labels: labels.value,
  datasets: props.series.map((prayer) => ({
    label: t(prayer.key),
    data: prayerData.value.map((entry) => timeToHours(entry[prayer.key])),
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
</script>

<style scoped>
.card {
  background: #fff;
  border-radius: 24px;
  padding: 24px;
  min-height: 480px;
  box-shadow: 0 18px 45px rgba(15, 23, 42, 0.12);
}

@media (max-width: 720px) {
  .card {
    padding: 16px;
  }
}
</style>
