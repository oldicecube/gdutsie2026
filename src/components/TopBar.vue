<script setup>
import { Github } from 'lucide-vue-next'

const sections = [
  { id: 'overview', label: '总览' },
  { id: 'profile', label: '第一印象' },
  { id: 'programs', label: '学习方向' },
  { id: 'birthdays', label: '生日' },
  { id: 'talents', label: '兴趣技能' },
  { id: 'special', label: '特色' }
]
</script>

<template>
  <header class="top-bar">
    <div class="top-inner">
      <div class="brand">
        <span>2026</span>
        <div>
          <strong>新生画像 · 2026</strong>
          <p>国际教育学院 | 数据可视化</p>
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
          <span>项目仓库</span>
        </a>
      </nav>
    </div>
  </header>
</template>
