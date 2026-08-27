<script setup>
import { computed } from 'vue'

const props = defineProps({
  items: { type: Array, required: true },
  metric: { type: String, default: '占比' },
  unit: { type: String, default: '%' },
  selected: { type: String, default: '' }
})

const emit = defineEmits(['select'])

const max = computed(() => Math.max(...props.items.map(item => item.value)))
const min = computed(() => Math.min(...props.items.map(item => item.value)))

function intensity(value) {
  if (max.value === min.value) return 0.72
  return 0.34 + ((value - min.value) / (max.value - min.value)) * 0.66
}

function fontSize(value) {
  if (max.value === min.value) return 1.05
  return 0.76 + ((value - min.value) / (max.value - min.value)) * 1.18
}
</script>

<template>
  <div class="text-cloud" aria-label="标签占比探索">
    <button
      v-for="item in items"
      :key="item.name"
      type="button"
      class="heat-word"
      :class="{ active: selected === item.name }"
      :style="{
        fontSize: `${fontSize(item.value)}rem`,
        opacity: intensity(item.value)
      }"
      :title="`${item.name}：${item.value}${unit}`"
      @click="emit('select', item.name)"
    >
      <span>{{ item.name }}</span>
      <small>{{ item.value }}{{ unit }}</small>
    </button>
  </div>
</template>
