<script setup>
import { computed } from 'vue'
import {
  GraduationCap,
  LocateFixed,
  MapPin,
  School,
  Users
} from 'lucide-vue-next'

const props = defineProps({
  label: { type: String, required: true },
  value: { type: Number, required: true },
  unit: { type: String, default: '' },
  icon: { type: String, default: 'users' }
})

const icons = { MapPin, LocateFixed, School, GraduationCap, Users, 'map-pin': MapPin, 'locate-fixed': LocateFixed, school: School, 'graduation-cap': GraduationCap, users: Users }
const iconComponent = computed(() => icons[props.icon] ?? Users)
</script>

<template>
  <div class="stat-tile">
    <div class="stat-icon">
      <component :is="iconComponent" :size="22" />
    </div>
    <div>
      <strong>{{ value }}<span>{{ unit }}</span></strong>
      <p>{{ label }}</p>
    </div>
  </div>
</template>
