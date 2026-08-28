<script setup>
import { computed, ref } from 'vue'
import {
  Activity,
  ArrowDown,
  BarChart3,
  Cake,
  GraduationCap,
  Sparkles,
  Users
} from 'lucide-vue-next'
import TopBar from './components/TopBar.vue'
import NeuCard from './components/NeuCard.vue'
import EChart from './components/EChart.vue'
import WordCloud from './components/WordCloud.vue'
import StatTile from './components/StatTile.vue'
import {
  birthMonths,
  birthYears,
  caveats,
  coverage,
  ethnicGroups,
  gender,
  politicalStatus,
  programs,
  provinces,
  regions,
  repeatedBirthdays,
  surnames,
  talents,
  zodiacSigns
} from './data/newStudentData'

const selected = ref({})

const birthdayCloudItems = computed(() =>
  repeatedBirthdays
    .flatMap(group => group.dates.map(date => ({
      name: date.replace(/^20/, '').replace('-', '/').replace('-', '/'),
      value: group.count,
      count: group.count,
      metric: '同天生日人数'
    })))
    .sort((a, b) => b.value - a.value || a.name.localeCompare(b.name))
)

const selectedItem = (key, items) => items.find(item => item.name === selected.value[key]) ?? null

const palette = ['#135fb8', '#ef7d32', '#16a394', '#6f62c2', '#d39b20', '#d45c5c']
const tooltip = {
  trigger: 'item',
  backgroundColor: 'rgba(255, 255, 255, .98)',
  borderColor: '#d7e2ee',
  borderWidth: 1,
  textStyle: { color: '#1c3553' },
  extraCssText: 'box-shadow: 0 12px 30px rgba(32, 61, 92, .16); border-radius: 10px;'
}

function donutOption(items, colors = palette, roseType = undefined) {
  return {
    color: colors,
    tooltip: { ...tooltip, formatter: params => `${params.name}<br /><strong>${params.value}%</strong>` },
    series: [{
      type: 'pie',
      radius: ['48%', '76%'],
      center: ['50%', '50%'],
      ...(roseType ? { roseType } : {}),
      label: { color: '#405875', formatter: '{b}\n{c}%' },
      labelLine: { lineStyle: { color: '#b7c8d9' } },
      itemStyle: { borderRadius: 9, borderColor: '#fff', borderWidth: 4 },
      emphasis: { scaleSize: 7, label: { fontWeight: 800 } },
      data: items
    }]
  }
}

const genderOption = computed(() => donutOption(gender, ['#135fb8', '#ef7d32']))
const politicalOption = computed(() => donutOption(politicalStatus, ['#16a394', '#6f62c2'], 'radius'))
const birthYearOption = computed(() => donutOption(
  birthYears.map(item => ({ ...item, name: item.name.slice(2, 4) })),
  ['#135fb8', '#ef7d32']
))
const birthMonthOption = computed(() => donutOption(birthMonths, [
  '#135fb8', '#2d75c4', '#4b8bd0', '#67a1d8', '#81b8dc', '#8fc8be',
  '#6fb6a0', '#ef7d32', '#e7944e', '#d9a65b', '#c79b63', '#9a8bc9'
]))
</script>

<template>
  <TopBar />

  <main class="page-shell">
    <section class="hero-section">
      <div class="hero-copy">
        <p class="hero-kicker">INTERNATIONAL EDUCATION COLLEGE <span>/</span> 2026</p>
        <h1>认识 <em>2026</em><br />从数据开始</h1>
        <p class="hero-lede">广东工业大学国际教育学院 2026 级新生数据看板。让来源、方向、生日与兴趣，汇成一张清晰而有温度的集体画像。</p>
        <a class="hero-action" href="#profile">浏览新生画像 <ArrowDown :size="17" /></a>
      </div>
      <div class="hero-orbit" aria-hidden="true">
        <div class="orbit-ring ring-one"></div>
        <div class="orbit-ring ring-two"></div>
        <div class="orbit-core">
          <span>2026</span>
          <strong>新生<br />数据</strong>
        </div>
        <span class="orbit-label label-one">兴趣</span>
        <span class="orbit-label label-two">相遇</span>
        <span class="orbit-label label-three">远方</span>
      </div>
    </section>

    <section id="overview" class="section intro-section">
      <div class="section-head">
        <span class="section-icon"><BarChart3 :size="24" /></span>
        <div>
          <p>资料一览</p>
          <h2>从一份资料，看见这一届的共同点</h2>
        </div>
      </div>

      <div class="coverage-grid">
        <StatTile
          v-for="item in coverage"
          :key="item.label"
          :label="item.label"
          :value="item.value"
          :unit="item.unit"
          :icon="item.icon"
        />
      </div>

      <div class="caveat-panel">
        <ul>
          <li v-for="item in caveats" :key="item">{{ item }}</li>
        </ul>
      </div>
    </section>

    <section id="profile" class="section">
      <div class="section-head">
        <span class="section-icon"><Users :size="24" /></span>
        <div>
          <p>新生画像</p>
          <h2>来自哪里，是什么样的人</h2>
        </div>
      </div>

      <div class="chart-grid">
        <NeuCard title="性别构成" subtitle="这一届同学的性别比例" badge="比例">
          <EChart :option="genderOption" height="320px" />
        </NeuCard>

        <NeuCard title="政治面貌" subtitle="这一届同学的政治面貌比例" badge="比例">
          <EChart :option="politicalOption" height="320px" />
        </NeuCard>

        <NeuCard title="省份来源" subtitle="来自不同省级地区的同学" badge="词云">
          <WordCloud
            :items="provinces"
            metric="占比"
            unit="%"
            :selected="selected.province"
            @select="value => (selected.province = value)"
          />
        </NeuCard>

        <NeuCard title="生源地区" subtitle="按城市与区域汇总的来源比例" badge="词云">
          <WordCloud
            :items="regions"
            metric="占比"
            unit="%"
            :selected="selected.region"
            @select="value => (selected.region = value)"
          />
        </NeuCard>
      </div>
    </section>

    <section id="programs" class="section">
      <div class="section-head">
        <span class="section-icon"><GraduationCap :size="24" /></span>
        <div>
          <p>学习方向</p>
          <h2>在国际化项目中探索专业兴趣</h2>
        </div>
      </div>

      <NeuCard title="专业项目分布" subtitle="不同专业项目的选择比例，名称越突出代表占比越高" badge="词云">
        <WordCloud
          :items="programs"
          metric="占比"
          unit="%"
          :selected="selected.program"
          @select="value => (selected.program = value)"
        />
      </NeuCard>
    </section>

    <section id="birthdays" class="section">
      <div class="section-head">
        <span class="section-icon"><Cake :size="24" /></span>
        <div>
          <p>生日档案</p>
          <h2>同年同月，也会在同一天相遇</h2>
        </div>
      </div>

      <div class="chart-grid">
        <NeuCard title="出生年份" subtitle="资料中保留的 07、08 年出生比例" badge="比例">
          <EChart :option="birthYearOption" height="320px" />
        </NeuCard>

        <NeuCard title="出生月份" subtitle="一年十二个月的生日分布" badge="饼图">
          <EChart :option="birthMonthOption" height="320px" />
        </NeuCard>
      </div>

      <NeuCard title="同天生日人数" subtitle="日期越突出，代表同一天生日的同学越多；悬浮可查看具体人数" badge="词云">
        <WordCloud
          :items="birthdayCloudItems"
          metric="同天生日人数"
          unit="人"
          :show-count="true"
          :selected="selected.birthday"
          @select="value => (selected.birthday = value)"
        />
      </NeuCard>
    </section>

    <section id="talents" class="section">
      <div class="section-head">
        <span class="section-icon"><Sparkles :size="24" /></span>
        <div>
          <p>兴趣与技能</p>
          <h2>每一种热爱，都可能成为新的连接</h2>
        </div>
      </div>

      <NeuCard title="兴趣与技能" subtitle="资料中出现的兴趣与技能标签，大小、颜色与位置共同反映占比" badge="词云">
        <WordCloud
          :items="talents"
          metric="占比"
          unit="%"
          :selected="selected.talent"
          @select="value => (selected.talent = value)"
        />
      </NeuCard>
    </section>

    <section id="special" class="section">
      <div class="section-head">
        <span class="section-icon"><Activity :size="24" /></span>
        <div>
          <p>特色数据</p>
          <h2>多元来源，也有各自鲜明的标签</h2>
        </div>
      </div>

      <div class="chart-grid">
        <NeuCard title="民族构成" subtitle="不同民族共同组成这一届新生" badge="词云">
          <WordCloud
            :items="ethnicGroups"
            metric="占比"
            unit="%"
            :show-count="true"
            :selected="selected.ethnic"
            @select="value => (selected.ethnic = value)"
          />
        </NeuCard>

        <NeuCard title="星座分布" subtitle="十二星座在这一届的比例" badge="词云">
          <WordCloud
            :items="zodiacSigns"
            metric="占比"
            unit="%"
            :selected="selected.zodiac"
            @select="value => (selected.zodiac = value)"
          />
        </NeuCard>

        <NeuCard title="姓氏比例" subtitle="全部已整理姓氏的出现比例" badge="词云">
          <WordCloud
            :items="surnames"
            metric="占比"
            unit="%"
            :selected="selected.surname"
            @select="value => (selected.surname = value)"
          />
        </NeuCard>
      </div>
    </section>

    <footer class="page-footer">
      <p>广东工业大学国际教育学院 · 2026 级新生大数据看板</p>
      <p>公开展示版</p>
    </footer>
  </main>
</template>
