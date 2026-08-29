<script setup>
import { computed } from 'vue'
import {
  Activity,
  ArrowDown,
  BarChart3,
  Cake,
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
  chineseZodiac,
  coverage,
  ethnicGroups,
  gender,
  politicalStatus,
  provinces,
  regions,
  repeatedBirthdays,
  namePhraseAnalysis,
  surnames,
  talents,
  zodiacSigns
} from './data/newStudentData'

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

const palette = ['#2457a6', '#d95f39', '#187f72', '#7a5aa6', '#d39b20', '#bd4652']
const tooltip = {
  trigger: 'item',
  backgroundColor: '#fffdf7',
  borderColor: '#202b3d',
  borderWidth: 2,
  textStyle: { color: '#202b3d' },
  extraCssText: 'box-shadow: 4px 4px 0 #202b3d; border-radius: 5px;'
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
      label: {
        color: '#39465a',
        formatter: '{b}\n{c}%',
        width: 88,
        overflow: 'breakAll',
        lineHeight: 16,
        fontSize: 11,
        align: 'center'
      },
      labelLine: { lineStyle: { color: '#8390a2', width: 2 } },
      itemStyle: { borderRadius: 2, borderColor: '#fffdf7', borderWidth: 3 },
      emphasis: { scaleSize: 7, label: { fontWeight: 800 } },
      labelLayout: { hideOverlap: false, moveOverlap: 'shiftY' },
      data: items
    }]
  }
}

const genderOption = computed(() => donutOption(gender, ['#2457a6', '#d95f39']))
const politicalOption = computed(() => donutOption(politicalStatus, ['#187f72', '#7a5aa6'], 'radius'))
const birthYearOption = computed(() => donutOption(
  birthYears.map(item => ({ ...item, name: item.name.slice(2, 4) })),
  ['#2457a6', '#d95f39']
))
const birthMonthOption = computed(() => donutOption(birthMonths, [
  '#2457a6', '#416fba', '#5b86c4', '#759bd0', '#8bb3c2', '#7fb8a4',
  '#5f9c89', '#d95f39', '#df7c54', '#d39b20', '#b98d55', '#7a5aa6'
]))

const zodiacOption = computed(() => ({
  color: chineseZodiac.map(item => item.color),
  tooltip: {
    ...tooltip,
    formatter: params => `${params.name}\u5e74<br /><strong>${Number(params.value).toFixed(1)}%</strong>`
  },
  series: [
    {
      type: 'pie',
      radius: ['0%', '62%'],
      center: ['50%', '50%'],
      avoidLabelOverlap: true,
      itemStyle: {
        borderColor: '#fffdf7',
        borderWidth: 3,
        borderRadius: 2
      },
      label: {
        show: true,
        position: 'outside',
        color: '#39465a',
        fontSize: 12,
        fontWeight: 850,
        lineHeight: 17,
        formatter: params => `${params.name}\u5e74\n${Number(params.value).toFixed(1)}%`
      },
      labelLine: {
        show: true,
        length: 12,
        length2: 22,
        lineStyle: { color: '#8390a2', width: 2 }
      },
      labelLayout: {
        hideOverlap: false,
        moveOverlap: 'shiftY',
        draggable: false
      },
      emphasis: {
        scale: true,
        scaleSize: 7,
        label: { fontWeight: 950 }
      },
      data: chineseZodiac
    },
    {
      type: 'pie',
      radius: ['0%', '62%'],
      center: ['50%', '50%'],
      silent: true,
      z: 3,
      itemStyle: {
        color: 'transparent',
        borderWidth: 0
      },
      label: {
        show: true,
        position: 'inside',
        formatter: params => params.data.emoji,
        fontSize: 30,
        lineHeight: 34,
        align: 'center',
        verticalAlign: 'middle',
        overflow: 'none'
      },
      labelLine: { show: false },
      labelLayout: {
        hideOverlap: false,
        moveOverlap: 'shiftY'
      },
      data: chineseZodiac.map(item => ({
        ...item,
        itemStyle: { color: 'transparent', borderWidth: 0 },
        label: {
          fontSize: item.value >= 10 ? 32 : item.value >= 1 ? 24 : 18,
          lineHeight: item.value >= 10 ? 36 : item.value >= 1 ? 28 : 22
        }
      }))
    }
  ]
}))

</script>

<template>
  <TopBar />

  <a class="skip-link" href="#main-content">&#x8DF3;&#x8F6C;&#x5230;&#x4E3B;&#x8981;&#x5185;&#x5BB9;</a>

  <main id="main-content" class="page-shell">
    <section class="hero-section">
      <div class="hero-copy">
        <p class="hero-kicker">INTERNATIONAL EDUCATION COLLEGE <span>/</span> 2026</p>
        <h1>认识 <em>2026</em><br />从数据开始</h1>
        <p class="hero-lede">广东工业大学国际教育学院 2026 级新生数据看板。让来源、生日与兴趣，汇成一张清晰而有温度的集体画像。</p>
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
          />
        </NeuCard>

        <NeuCard title="生源地区" subtitle="按城市与区域汇总的来源比例" badge="词云">
          <WordCloud
            :items="regions"
            metric="占比"
            unit="%"
          />
        </NeuCard>
      </div>
    </section>

    <section id="birthdays" class="section">
      <div class="section-head">
        <span class="section-icon"><Cake :size="24" /></span>
        <div>
          <p>生日档案</p>
          <h2>同年同月，也会在同一天相遇</h2>
        </div>
      </div>

      <div class="chart-grid birthday-chart-grid">
        <NeuCard title="出生年份" subtitle="资料中保留的 07、08 年出生比例" badge="比例">
          <EChart :option="birthYearOption" height="320px" />
        </NeuCard>

        <NeuCard title="出生月份" subtitle="一年十二个月的生日分布" badge="饼图">
          <EChart :option="birthMonthOption" height="320px" />
        </NeuCard>

        <NeuCard title="生肖分布" subtitle="按农历春节边界计算；表情位于对应扇形内部" badge="饼图">
          <EChart :option="zodiacOption" height="320px" />
        </NeuCard>
      </div>

      <NeuCard title="同天生日人数" subtitle="日期越突出，代表同一天生日的同学越多；悬浮可查看具体人数" badge="词云">
        <WordCloud
          :items="birthdayCloudItems"
          metric="同天生日人数"
          unit="人"
          :show-count="true"
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
          />
        </NeuCard>

        <NeuCard title="星座分布" subtitle="十二星座在这一届的比例" badge="词云">
          <WordCloud
            :items="zodiacSigns"
            metric="占比"
            unit="%"
          />
        </NeuCard>

        <NeuCard title="姓氏比例" subtitle="全部已整理姓氏的出现比例" badge="词云">
          <WordCloud
            :items="surnames"
            metric="占比"
            unit="%"
          />
        </NeuCard>

        <NeuCard title="姓名里的学院印记" subtitle="按姓名中的每个汉字统计，不展示个人姓名" badge="字符统计">
          <div class="phrase-analysis">
            <div class="phrase-summary">
              <p class="phrase-label">目标短语</p>
              <strong class="phrase-text">{{ namePhraseAnalysis.phrase }}</strong>
              <p class="phrase-note">
                共有 {{ namePhraseAnalysis.matchingStudents }} 人的姓名中出现过这些字。
              </p>
            </div>

            <div class="phrase-table-wrap">
              <table class="phrase-table">
                <thead>
                  <tr>
                    <th>字</th>
                    <th>出现人数</th>
                    <th>占比</th>
                  </tr>
                </thead>
                <tbody>
                  <tr v-for="item in namePhraseAnalysis.characters" :key="item.name">
                    <th scope="row" class="phrase-character">{{ item.name }}</th>
                    <td>{{ item.count }}</td>
                    <td>{{ item.value.toFixed(2) }}%</td>
                  </tr>
                </tbody>
              </table>
            </div>
          </div>
        </NeuCard>
      </div>
    </section>

    <footer class="page-footer">
      <p>广东工业大学国际教育学院 · 2026 级新生大数据看板</p>
      <p>公开展示版</p>
    </footer>
  </main>
</template>
