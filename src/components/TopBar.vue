<script setup>
import { Github } from 'lucide-vue-next'

const sections = [
  { id: 'overview', label: '总览' },
  { id: 'profile', label: '基础画像' },
  { id: 'programs', label: '专业' },
  { id: 'birthdays', label: '生日' },
  { id: 'talents', label: '特长' },
  { id: 'special', label: '特色' }
]
</script>

<template>
  <header class="top-bar">
    <div class="top-inner">
      <div class="brand">
        <span>2026</span>
        <div>
          <strong>新生数据看板</strong>
          <p>国际教育学院 · 部分数据</p>
        </div>
      </div>
      <nav class="top-nav" aria-label="页面导航">
        <a v-for="section in sections" :key="section.id" :href="`#${section.id}`">
          {{ section.label }}
        </a>
        <a
          class="github-link"
          href="https://github.com/oldicecube/gdutsie2026"
          target="_blank"
          rel="noreferrer"
        >
          <Github :size="17" />
          <span>GitHub</span>
        </a>
      </nav>
    </div>
  </header>
</template>
