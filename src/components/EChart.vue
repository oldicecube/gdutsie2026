<script setup>
import { onBeforeUnmount, onMounted, ref, watch } from 'vue'
import * as echarts from 'echarts'

const props = defineProps({
  option: { type: Object, required: true },
  height: { type: String, default: '320px' }
})

const chartEl = ref(null)
let chart = null
let resizeObserver = null

onMounted(() => {
  chart = echarts.init(chartEl.value, null, { renderer: 'svg' })
  chart.setOption(props.option)
  resizeObserver = new ResizeObserver(() => chart.resize())
  resizeObserver.observe(chartEl.value)
})

watch(
  () => props.option,
  option => chart?.setOption(option, true),
  { deep: true }
)

onBeforeUnmount(() => {
  resizeObserver?.disconnect()
  chart?.dispose()
})
</script>

<template>
  <div ref="chartEl" class="echart" :style="{ height }" />
</template>
