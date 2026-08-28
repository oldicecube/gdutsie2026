<script setup>
import { computed, ref } from 'vue'

const props = defineProps({
  items: { type: Array, required: true },
  metric: { type: String, default: '??' },
  unit: { type: String, default: '%' },
  selected: { type: String, default: '' },
  showCount: { type: Boolean, default: false }
})

const emit = defineEmits(['select'])
const hovered = ref('')

const sortedItems = computed(() => [...props.items].sort((a, b) => {
  const valueDiff = Number(b.value) - Number(a.value)
  return valueDiff || String(a.name).localeCompare(String(b.name), 'zh-CN')
}))
const max = computed(() => Math.max(...sortedItems.value.map(item => Number(item.value)), 0))
const min = computed(() => Math.min(...sortedItems.value.map(item => Number(item.value)), max.value))
const coreItem = computed(() => sortedItems.value[0] ?? null)
const surroundingItems = computed(() => sortedItems.value.slice(1))

function ratio(value) {
  if (max.value === min.value) return 0.72
  return 0.28 + ((Number(value) - min.value) / (max.value - min.value)) * 0.72
}

function fontSize(value, core = false) {
  if (core) return 'clamp(1.35rem, 3vw, 2.2rem)'
  const size = 0.78 + ((Number(value) - min.value) / Math.max(max.value - min.value, 1)) * 1.15
  return `${size.toFixed(2)}rem`
}

function label(item) {
  return props.showCount && item.count != null
    ? `${item.name} ? ${item.count}?`
    : `${item.name} ? ${item.value}${props.unit}`
}

function tooltip(item) {
  const lines = [`${item.name}`, `${props.metric}?${item.value}${props.unit}`]
  if (item.count != null) lines.push(`???${item.count}?`)
  return lines.join('\n')
}

function heatStyle(item, core = false) {
  const alpha = ratio(item.value)
  return {
    color: `rgba(19, 95, 184, ${alpha.toFixed(2)})`,
    backgroundColor: `rgba(19, 95, 184, ${(0.035 + alpha * 0.12).toFixed(2)})`,
    fontSize: fontSize(item.value, core)
  }
}
</script>

<template>
  <div class="text-cloud" aria-label="?????">
    <button
      v-if="coreItem"
      type="button"
      class="heat-word heat-core"
      :class="{ active: selected === coreItem.name }"
      :style="heatStyle(coreItem, true)"
      :aria-label="label(coreItem)"
      :title="tooltip(coreItem)"
      @mouseenter="hovered = coreItem.name"
      @mouseleave="hovered = ''"
      @focus="hovered = coreItem.name"
      @blur="hovered = ''"
      @click="emit('select', coreItem.name)"
    >
      <span>{{ coreItem.name }}</span>
    </button>

    <div class="heat-ring">
      <button
        v-for="item in surroundingItems"
        :key="item.name"
        type="button"
        class="heat-word"
        :class="{ active: selected === item.name }"
        :style="heatStyle(item)"
        :aria-label="label(item)"
        :title="tooltip(item)"
        @mouseenter="hovered = item.name"
        @mouseleave="hovered = ''"
        @focus="hovered = item.name"
        @blur="hovered = ''"
        @click="emit('select', item.name)"
      >
        <span>{{ item.name }}</span>
      </button>
    </div>

    <div v-if="hovered" class="heat-hover-tip" role="status">
      <template v-for="item in sortedItems" :key="item.name">
        <span v-if="item.name === hovered">
          <strong>{{ item.name }}</strong>
          <small>{{ metric }}?{{ item.value }}{{ unit }}<template v-if="item.count != null"> ? ???{{ item.count }}?</template></small>
        </span>
      </template>
    </div>
  </div>
</template>
