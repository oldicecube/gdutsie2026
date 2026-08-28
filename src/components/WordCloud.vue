<script setup>
import { nextTick, onBeforeUnmount, onMounted, ref, watch } from 'vue'
import * as echarts from 'echarts'
import 'echarts-wordcloud'

const props = defineProps({
  items: { type: Array, required: true },
  metric: { type: String, default: '占比' },
  unit: { type: String, default: '%' },
  showCount: { type: Boolean, default: false },
  height: { type: String, default: '320px' }
})

const chartEl = ref(null)
const hovered = ref(null)
let chart = null
let resizeObserver = null
let renderFrame = 0
let lastSize = ''

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

function getOption() {
  const items = sortedItems()
  const width = chartEl.value?.clientWidth || 600
  const height = chartEl.value?.clientHeight || 320
  const dense = items.length > 40
  const compact = items.length > 20
  const narrow = width < 500
  const sparse = items.length <= 15
  const hasLongLabels = items.some(item => String(item.name).length > 10)
  const maxSize = dense
    ? (narrow ? 35 : 54)
    : hasLongLabels
      ? (narrow ? 38 : 58)
      : sparse
        ? (narrow ? 62 : 84)
        : (narrow ? 52 : 68)
  const minSize = dense
    ? (narrow ? 14 : 16)
    : hasLongLabels
      ? (narrow ? 13 : 16)
      : (narrow ? 17 : 19)
  const rotation = dense || narrow || hasLongLabels ? [0, 0] : [-25, 25]
  const valueExtent = items.reduce(
    (extent, item) => [Math.min(extent[0], Number(item.value)), Math.max(extent[1], Number(item.value))],
    [Infinity, -Infinity]
  )
  const valueSpan = Math.max(valueExtent[1] - valueExtent[0], 0.0001)

  // Give dense clouds a predictable floor so singleton surnames remain legible
  // instead of being repeatedly shrunk by the layout engine.
  const canvasPadding = dense ? 4 : 6

  const getFontSize = item => {
    const normalized = Math.pow((Number(item.value) - valueExtent[0]) / valueSpan, compact ? 0.62 : 0.78)
    return Math.round(minSize + normalized * (maxSize - minSize))
  }

  return {
    animation: false,
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
      shape: 'square',
      left: canvasPadding,
      top: canvasPadding,
      width: Math.max(width - canvasPadding * 2, 0),
      height: Math.max(height - canvasPadding * 2, 0),
      sizeRange: [minSize, maxSize],
      rotationRange: rotation,
      rotationStep: 15,
      gridSize: 4,
      drawOutOfBound: false,
      shrinkToFit: true,
      layoutAnimation: false,
      textStyle: {
        fontFamily: 'Inter, HarmonyOS Sans SC, PingFang SC, Microsoft YaHei, sans-serif',
        fontWeight: 750,
        color: params => params.data?.textStyle?.color || palette[items.findIndex(item => item.name === params.name) % palette.length]
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
          fontSize: getFontSize(item),
          color: item.textStyle?.color || palette[index % palette.length],
          fontWeight: 750
        }
      }))
    }]
  }
}

function render() {
  if (!chart || !chartEl.value?.clientWidth || !chartEl.value?.clientHeight) return
  chart.clear()
  chart.setOption(getOption(), { notMerge: true, lazyUpdate: false })
  chart.resize({ animation: false })
}

function queueRender(force = false) {
  cancelAnimationFrame(renderFrame)
  renderFrame = requestAnimationFrame(() => {
    const size = `${chartEl.value?.clientWidth || 0}x${chartEl.value?.clientHeight || 0}`
    if (force || size !== lastSize) {
      lastSize = size
      render()
    }
  })
}

onMounted(async () => {
  await nextTick()
  chart = echarts.init(chartEl.value, null, { renderer: 'canvas' })
  chart.on('mouseover', params => {
    if (params.data) hovered.value = params.data
  })
  chart.on('mouseout', () => {
    hovered.value = null
  })

  await new Promise(resolve => requestAnimationFrame(() => requestAnimationFrame(resolve)))
  queueRender(true)
  resizeObserver = new ResizeObserver(() => queueRender())
  resizeObserver.observe(chartEl.value)
})

watch(
  () => [props.items, props.metric, props.unit, props.showCount],
  () => queueRender(true),
  { deep: true }
)

onBeforeUnmount(() => {
  cancelAnimationFrame(renderFrame)
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
