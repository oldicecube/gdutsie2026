<script setup>
import { Github } from 'lucide-vue-next'

const sections = [
  { id: 'overview', label: '总览' },
  { id: 'profile', label: '新生画像' },
  { id: 'birthdays', label: '生日档案' },
  { id: 'talents', label: '兴趣技能' },
  { id: 'special', label: '特色数据' }
]
</script>

<template>
  <header class="top-bar">
    <div class="top-inner">
      <a class="brand" href="#overview" aria-label="返回看板总览">
        <span class="brand-mark">GDUT</span>
        <div>
          <strong>广东工业大学 · 国际教育学院</strong>
          <p>2026级新生大数据看板</p>
        </div>
      </a>
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
          <Github :size="16" />
          <span>项目仓库</span>
        </a>
      </nav>
    </div>
  </header>
</template>
