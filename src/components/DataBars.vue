<script setup>
const props = defineProps({
  items: { type: Array, required: true },
  unit: { type: String, default: '%' }
})

const max = Math.max(...props.items.map(item => item.value))
</script>

<template>
  <div class="data-bars">
    <div v-for="item in items" :key="item.name" class="bar-row">
      <span class="bar-label">{{ item.name }}</span>
      <span class="bar-track">
        <span
          class="bar-fill"
          :style="{ width: `${(item.value / max) * 100}%` }"
        />
      </span>
      <span class="bar-value">{{ item.value }}{{ unit }}</span>
    </div>
  </div>
</template>
