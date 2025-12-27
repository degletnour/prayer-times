<template>
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
      <span v-for="item in series" :key="item.key" class="legend-item">
        <span class="legend-dot" :style="{ backgroundColor: item.color }"></span>
        {{ t(item.key) }}
      </span>
    </div>
  </header>
</template>

<script setup>
import { inject } from "vue";

const { language, t } = inject("i18n");

defineProps({
  series: {
    type: Array,
    required: true,
  },
});
</script>

<style scoped>
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
</style>
