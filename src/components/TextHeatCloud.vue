<script setup>
import { computed, ref } from 'vue'

const props = defineProps({
  items: { type: Array, required: true },
  metric: { type: String, default: '占比' },
  unit: { type: String, default: '%' },
  selected: { type: String, default: '' },
  showCount: { type: Boolean, default: false }
})

const emit = defineEmits(['select'])
const hovered = ref(null)

const sortedItems = computed(() => [...props.items].sort((a, b) => {
  const valueDiff = Number(b.value) - Number(a.value)
  return valueDiff || String(a.name).localeCompare(String(b.name), 'zh-CN')
}))
const max = computed(() => Math.max(...sortedItems.value.map(item => Number(item.value)), 0))
const min = computed(() => Math.min(...sortedItems.value.map(item => Number(item.value)), max.value))
const leadItem = computed(() => sortedItems.value[0] ?? null)
const remainingItems = computed(() => sortedItems.value.slice(1))

function normalized(value) {
  if (max.value === min.value) return 0.72
  return 0.26 + ((Number(value) - min.value) / (max.value - min.value)) * 0.74
}

function heatStyle(item) {
  const intensity = normalized(item.value)
  const size = 0.82 + intensity * 0.78
  const weight = Math.round(520 + intensity * 280)
  // Follow the reference token heatmap: low values stay mint/teal, while
  // higher values move toward a deeper amber/red treatment.
  const hue = Math.round(178 - intensity * 164)
  const lightness = Math.round(48 - intensity * 18)
  const bgLightness = Math.round(93 - intensity * 17)
  return {
    '--heat-color': `hsl(${hue} 72% ${lightness}%)`,
    '--heat-bg': `hsl(${hue} 72% ${bgLightness}%)`,
    '--heat-size': `${size.toFixed(2)}rem`,
    '--heat-weight': weight
  }
}

function accessibleLabel(item) {
  const count = item.count != null && props.unit !== '人' ? `，${item.count}人` : ''
  return `${item.name}，${props.metric}${item.value}${props.unit}${count}`
}

function tooltip(item) {
  const count = props.showCount && item.count != null && props.unit !== '人' ? ` · ${item.count}人` : ''
  return `${item.name}　${props.metric} ${item.value}${props.unit}${count}`
}
</script>

<template>
  <div class="text-cloud" :aria-label="`${metric}文字热力图`">
    <div class="text-cloud__canvas">
      <div v-if="leadItem" class="text-cloud__lead">
        <button
          type="button"
          class="heat-word heat-word--lead"
          :class="{ active: selected === leadItem.name }"
          :style="heatStyle(leadItem)"
          :aria-label="accessibleLabel(leadItem)"
          :title="tooltip(leadItem)"
          @mouseenter="hovered = leadItem"
          @mouseleave="hovered = null"
          @focus="hovered = leadItem"
          @blur="hovered = null"
          @click="emit('select', leadItem.name)"
        >
          <span>{{ leadItem.name }}</span>
        </button>
      </div>

      <div class="text-cloud__flow">
        <button
          v-for="item in remainingItems"
          :key="item.name"
          type="button"
          class="heat-word"
          :class="{ active: selected === item.name }"
          :style="heatStyle(item)"
          :aria-label="accessibleLabel(item)"
          :title="tooltip(item)"
          @mouseenter="hovered = item"
          @mouseleave="hovered = null"
          @focus="hovered = item"
          @blur="hovered = null"
          @click="emit('select', item.name)"
          >
            <span>{{ item.name }}</span>
          </button>
      </div>
    </div>

    <Transition name="heat-tip">
      <div v-if="hovered" class="heat-hover-tip" role="status" aria-live="polite">
        <strong>{{ hovered.name }}</strong>
        <span v-if="unit === '人'">{{ hovered.value }}人</span>
        <span v-else>{{ metric }} {{ hovered.value }}{{ unit }}</span>
        <span v-if="unit !== '人' && showCount && hovered.count != null">{{ hovered.count }}人</span>
      </div>
    </Transition>
  </div>
</template>
