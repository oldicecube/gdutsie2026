<script setup>
import { onBeforeUnmount, onMounted, ref, watch } from 'vue'
import * as echarts from 'echarts'
import 'echarts-wordcloud'

const props = defineProps({
  items: { type: Array, required: true },
  metric: { type: String, default: '占比' },
  unit: { type: String, default: '%' },
  selected: { type: String, default: '' },
  showCount: { type: Boolean, default: false },
  height: { type: String, default: '320px' }
})

const emit = defineEmits(['select'])
const chartEl = ref(null)
const hovered = ref(null)
let chart = null
let resizeObserver = null

const palette = [
  '#2457a6', '#187f72', '#d95f39', '#7a5aa6', '#d39b20',
  '#287f68', '#bd4652', '#416fba', '#8b68a8', '#b07832'
]

const sortedItems = () => [...props.items]
  .filter(item => item && item.name != null && Number.isFinite(Number(item.value)))
  .sort((a, b) => Number(b.value) - Number(a.value) || String(a.name).localeCompare(String(b.name), 'zh-CN'))

function formatValue(item) {
  if (props.unit === '人') return `${item.value}${props.unit}`
  return `${props.metric} ${item.value}${props.unit}`
}

function formatCount(item) {
  if (!props.showCount || item.count == null || props.unit === '人') return ''
  return ` · ${item.count}人`
}

function tooltipFormatter(params) {
  const item = params?.data ?? params
  if (!item) return ''
  return `<div class="word-cloud-tooltip"><strong>${item.name}</strong><span>${formatValue(item)}${formatCount(item)}</span></div>`
}

function colorFor(item, index) {
  if (props.selected && item.name === props.selected) return '#d95f39'
  return item.textStyle?.color || palette[index % palette.length]
}

function getOption() {
  const items = sortedItems()
  const dense = items.length > 40
  const canvasWidth = chartEl.value?.clientWidth || 600
  const wide = canvasWidth > 700
  const hasLongLabels = items.some(item => String(item.name).length > 10)
  const maxSize = dense ? 46 : hasLongLabels ? 42 : 58
  const minSize = dense ? 12 : hasLongLabels ? 14 : 16

  return {
    animation: true,
    animationDuration: 650,
    animationDurationUpdate: 420,
    tooltip: {
      trigger: 'item',
      confine: true,
      padding: [9, 12],
      backgroundColor: '#fffdf7',
      borderColor: '#202b3d',
      borderWidth: 2,
      textStyle: { color: '#202b3d', fontFamily: 'inherit' },
      extraCssText: 'box-shadow: 4px 4px 0 #202b3d; border-radius: 5px;',
      formatter: tooltipFormatter
    },
    series: [{
      type: 'wordCloud',
      shape: wide ? 'square' : 'circle',
      left: 'center',
      top: 'center',
      width: '94%',
      height: '90%',
      sizeRange: [minSize, maxSize],
      rotationRange: hasLongLabels ? [0, 0] : [-25, 25],
      rotationStep: 15,
      gridSize: dense ? 5 : 8,
      drawOutOfBound: false,
      shrinkToFit: true,
      layoutAnimation: items.length < 80,
      textStyle: {
        fontFamily: 'Inter, HarmonyOS Sans SC, PingFang SC, Microsoft YaHei, sans-serif',
        fontWeight: 750,
        color: params => colorFor(params, items.findIndex(item => item.name === params.name))
      },
      emphasis: {
        focus: 'self',
        textStyle: {
          fontWeight: 900,
          textShadowBlur: 8,
          textShadowColor: 'rgba(32, 43, 61, .32)'
        }
      },
      data: items.map((item, index) => ({
        ...item,
        value: Number(item.value),
        textStyle: {
          ...(item.textStyle || {}),
          color: colorFor(item, index),
          fontWeight: props.selected === item.name ? 900 : 750
        }
      }))
    }]
  }
}

function render() {
  if (!chart) return
  chart.setOption(getOption(), true)
  chart.resize()
}

function handleResize() {
  render()
}

onMounted(() => {
  chart = echarts.init(chartEl.value, null, { renderer: 'canvas' })
  chart.on('mouseover', params => {
    if (params.data) hovered.value = params.data
  })
  chart.on('mouseout', () => {
    hovered.value = null
  })
  chart.on('click', params => {
    if (params.data?.name != null) emit('select', params.data.name)
  })
  render()
  resizeObserver = new ResizeObserver(handleResize)
  resizeObserver.observe(chartEl.value)
})

watch(
  () => [props.items, props.selected, props.metric, props.unit, props.showCount],
  render,
  { deep: true }
)

onBeforeUnmount(() => {
  resizeObserver?.disconnect()
  chart?.dispose()
})
</script>

<template>
  <div class="word-cloud-shell">
    <div
      ref="chartEl"
      class="word-cloud"
      :style="{ height }"
      :aria-label="`${metric}词云`"
      role="img"
    />
    <div v-if="hovered" class="word-cloud__sr-status" role="status" aria-live="polite">
      {{ hovered.name }}，{{ formatValue(hovered) }}{{ formatCount(hovered) }}
    </div>
  </div>
</template>




