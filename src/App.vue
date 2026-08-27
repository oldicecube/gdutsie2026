<script setup>
import { computed, ref } from 'vue'
import {
  Activity,
  ArrowDown,
  Cake,
  GraduationCap,
  Sparkles,
  Users,
  BarChart3
} from 'lucide-vue-next'
import TopBar from './components/TopBar.vue'
import NeuCard from './components/NeuCard.vue'
import EChart from './components/EChart.vue'
import DataBars from './components/DataBars.vue'
import TextHeatCloud from './components/TextHeatCloud.vue'
import StatTile from './components/StatTile.vue'
import {
  birthMonths,
  birthYears,
  candidateTypes,
  caveats,
  coverage,
  detailedRegions,
  ethnicGroups,
  gender,
  nameLengths,
  nonLocalStudents,
  politicalStatus,
  programs,
  provinces,
  publicDimensions,
  references,
  regions,
  repeatedBirthdays,
  schools,
  schoolStructure,
  specialDates,
  surnames,
  surnameStructure,
  talents,
  zodiacSigns
} from './data/newStudentData'

const selectedTalent = ref('')
const selectedBirthday = ref('')
const selectedEthnic = ref('')

const birthdayCloudItems = computed(() =>
  repeatedBirthdays
    .flatMap(group => group.dates.map(date => ({ name: date, value: group.count })))
    .sort((a, b) => b.value - a.value || a.name.localeCompare(b.name))
)

const palette = ['#2563eb', '#f97316', '#14b8a6', '#a855f7', '#eab308', '#ef4444']
const tooltip = {
  trigger: 'item',
  backgroundColor: 'rgba(248, 250, 252, .96)',
  borderColor: '#dbe3ee',
  borderWidth: 1,
  textStyle: { color: '#263243' },
  extraCssText: 'box-shadow: 8px 8px 20px rgba(72, 84, 108, .18); border-radius: 14px;'
}

const axisStyle = {
  axisLine: { lineStyle: { color: '#b8c2d0' } },
  axisTick: { show: false },
  axisLabel: { color: '#64748b' },
  splitLine: { lineStyle: { color: '#e2e8f0' } }
}

const genderOption = computed(() => ({
  color: palette,
  tooltip: { ...tooltip, formatter: '{b}<br />{c}%' },
  series: [
    {
      type: 'pie',
      radius: ['48%', '76%'],
      center: ['50%', '50%'],
      label: { color: '#475569', formatter: '{b}\n{c}%' },
      labelLine: { lineStyle: { color: '#b8c2d0' } },
      itemStyle: { borderRadius: 10, borderColor: '#eef2f7', borderWidth: 4 },
      emphasis: { scaleSize: 8 },
      data: gender
    }
  ]
}))

const politicalOption = computed(() => ({
  color: palette,
  tooltip: { ...tooltip, formatter: '{b}<br />{c}%' },
  series: [
    {
      type: 'pie',
      roseType: 'radius',
      radius: ['24%', '76%'],
      center: ['50%', '50%'],
      label: { color: '#475569', formatter: '{b}\n{c}%' },
      labelLine: { lineStyle: { color: '#b8c2d0' } },
      itemStyle: { borderRadius: 8, borderColor: '#eef2f7', borderWidth: 3 },
      data: politicalStatus
    }
  ]
}))

const birthYearOption = computed(() => ({
  color: ['#14b8a6', '#2563eb'],
  tooltip: { ...tooltip, formatter: '{b}<br />{c}%' },
  series: [
    {
      type: 'pie',
      radius: ['50%', '78%'],
      center: ['50%', '50%'],
      label: { color: '#475569', formatter: '{b}\n{c}%' },
      labelLine: { lineStyle: { color: '#b8c2d0' } },
      itemStyle: { borderRadius: 12, borderColor: '#eef2f7', borderWidth: 5 },
      data: birthYears
    }
  ]
}))

function horizontalBarOption(items, color = '#2563eb') {
  return {
    color,
    tooltip: { ...tooltip, trigger: 'axis', formatter: '{b}<br />{c}%' },
    grid: { left: 8, right: 28, top: 10, bottom: 10, containLabel: true },
    xAxis: { type: 'value', ...axisStyle },
    yAxis: {
      type: 'category',
      inverse: true,
      data: items.map(item => item.name),
      ...axisStyle,
      axisLabel: { ...axisStyle.axisLabel, interval: 0 }
    },
    series: [
      {
        type: 'bar',
        data: items.map(item => item.value),
        barWidth: 14,
        itemStyle: { borderRadius: 8 },
        emphasis: { itemStyle: { shadowBlur: 14, shadowColor: 'rgba(37, 99, 235, .35)' } }
      }
    ]
  }
}

const provinceOption = computed(() => horizontalBarOption(provinces, '#2563eb'))
const regionOption = computed(() => horizontalBarOption(regions, '#f97316'))
const candidateOption = computed(() => horizontalBarOption(candidateTypes, '#a855f7'))
const programOption = computed(() => horizontalBarOption(programs, '#14b8a6'))
const zodiacOption = computed(() => horizontalBarOption(zodiacSigns, '#eab308'))
const surnameOption = computed(() => horizontalBarOption(surnames, '#ef4444'))
const schoolOption = computed(() => horizontalBarOption(schools, '#0ea5e9'))
const detailedRegionOption = computed(() => horizontalBarOption(detailedRegions, '#f97316'))

const birthMonthOption = computed(() => ({
  color: ['#2563eb'],
  tooltip: { ...tooltip, trigger: 'axis', formatter: '{b}<br />{c}%' },
  grid: { left: 8, right: 18, top: 20, bottom: 8, containLabel: true },
  xAxis: { type: 'category', data: birthMonths.map(item => item.name), ...axisStyle },
  yAxis: { type: 'value', ...axisStyle },
  series: [
    {
      type: 'line',
      smooth: true,
      symbolSize: 10,
      data: birthMonths.map(item => item.value),
      lineStyle: { width: 4 },
      areaStyle: {
        color: 'rgba(37, 99, 235, .12)'
      },
      emphasis: { focus: 'series' }
    }
  ]
}))

const talentRadarOption = computed(() => ({
  color: ['#f97316'],
  tooltip: { ...tooltip },
  radar: {
    indicator: talents.slice(0, 10).map(item => ({ name: item.name, max: 10 })),
    radius: '68%',
    center: ['50%', '52%'],
    axisName: { color: '#52627a' },
    splitLine: { lineStyle: { color: '#dbe3ee' } },
    splitArea: { areaStyle: { color: ['rgba(255,255,255,.55)', 'rgba(226,232,240,.45)'] } },
    axisLine: { lineStyle: { color: '#dbe3ee' } }
  },
  series: [
    {
      type: 'radar',
      data: [
        {
          value: talents.slice(0, 10).map(item => item.value),
          name: '特长占比',
          areaStyle: { color: 'rgba(249, 115, 22, .18)' },
          lineStyle: { width: 3 }
        }
      ],
      emphasis: { lineStyle: { width: 5 } }
    }
  ]
}))

const selectedTalentItem = computed(
  () => talents.find(item => item.name === selectedTalent.value) ?? null
)
const selectedBirthdayItem = computed(
  () => birthdayCloudItems.value.find(item => item.name === selectedBirthday.value) ?? null
)
const selectedEthnicItem = computed(
  () => ethnicGroups.find(item => item.name === selectedEthnic.value) ?? null
)
</script>

<template>
  <TopBar />

  <main class="page-shell">
    <section class="hero-section">
      <div class="hero-copy">
        <p class="hero-kicker">INTERNATIONAL EDUCATION COLLEGE · 2026</p>
        <h1>你好，<em>2026</em><br />我们在这里相遇</h1>
        <p class="hero-lede">一份关于新同学的公开数据画像，从来处、专业到兴趣与生日，看看我们共同组成的这一届。</p>
        <a class="hero-action" href="#profile">开始认识我们 <ArrowDown :size="17" /></a>
      </div>
      <div class="hero-orbit" aria-hidden="true">
        <div class="orbit-ring ring-one"></div>
        <div class="orbit-ring ring-two"></div>
        <div class="orbit-core">
          <span>2026</span>
          <strong>新生<br />画像</strong>
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
          <h2>先从这份资料，看见我们的共同点</h2>
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
          <p>第一印象</p>
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

        <NeuCard title="省份来源" subtitle="来自 7 个省级地区的同学" badge="占比">
          <EChart :option="provinceOption" height="330px" />
        </NeuCard>

        <NeuCard title="生源地区" subtitle="按城市与区域汇总的来源比例" badge="占比">
          <EChart :option="regionOption" height="330px" />
        </NeuCard>

        <NeuCard title="考生类别" subtitle="应届与往届、城乡来源的比例" badge="占比">
          <EChart :option="candidateOption" height="300px" />
        </NeuCard>
      </div>
    </section>

    <section id="programs" class="section">
      <div class="section-head">
        <span class="section-icon"><GraduationCap :size="24" /></span>
        <div>
          <p>学习方向</p>
          <h2>我们将在这里学习什么</h2>
        </div>
      </div>

      <NeuCard title="专业项目分布" subtitle="9 个专业项目的选择比例" badge="占比">
        <EChart :option="programOption" height="430px" />
      </NeuCard>
    </section>

    <section id="birthdays" class="section">
      <div class="section-head">
        <span class="section-icon"><Cake :size="24" /></span>
        <div>
          <p>生日档案</p>
          <h2>同年同月，也有同一天</h2>
        </div>
      </div>

      <div class="chart-grid">
        <NeuCard title="出生年份" subtitle="2007 与 2008 年出生的同学" badge="比例">
          <EChart :option="birthYearOption" height="320px" />
        </NeuCard>

        <NeuCard title="出生月份" subtitle="一年十二个月，都有我们的生日" badge="月份">
          <EChart :option="birthMonthOption" height="320px" />
        </NeuCard>
      </div>

      <NeuCard
        title="重复生日日期"
        subtitle="多个生日日期迎来不止一位同学，字号越大代表同日生日人数越多"
        badge="日期探索"
      >
        <p v-if="selectedBirthdayItem" class="selection-note">
          {{ selectedBirthdayItem.name }} · {{ selectedBirthdayItem.value }} 位同学同日生日
        </p>
        <TextHeatCloud
          :items="birthdayCloudItems"
          metric="人数"
          unit="人"
          :selected="selectedBirthday"
          @select="value => (selectedBirthday = value)"
        />
      </NeuCard>

      <div class="chart-grid">
        <NeuCard title="重复生日明细" subtitle="按同日生日人数从多到少排列，相同人数合并展示" badge="日期清单">
          <div class="table-wrap">
            <table>
              <thead>
                <tr>
                  <th>同日生日</th>
                  <th>日期</th>
                </tr>
              </thead>
              <tbody>
                <tr v-for="group in repeatedBirthdays" :key="group.count">
                  <td>{{ group.count }} 人</td>
                  <td>{{ group.dates.join('；') }}</td>
                </tr>
              </tbody>
            </table>
          </div>
        </NeuCard>

        <NeuCard title="特殊日期生日" subtitle="生日与特别日子相遇的比例" badge="节日清单">
          <div class="special-date-list">
            <div v-for="item in specialDates" :key="item.name" class="special-date-row">
              <span><strong>{{ item.date }}</strong>{{ item.name }}</span>
              <span>{{ item.value }}%</span>
            </div>
          </div>
        </NeuCard>
      </div>
    </section>

    <section id="talents" class="section">
      <div class="section-head">
        <span class="section-icon"><Sparkles :size="24" /></span>
        <div>
          <p>兴趣与技能</p>
          <h2>每个人都有自己的闪光点</h2>
        </div>
      </div>

      <div class="chart-grid">
        <NeuCard title="兴趣技能全景" subtitle="同一位同学可以拥有多个标签" badge="兴趣全景">
          <EChart :option="talentRadarOption" height="420px" />
        </NeuCard>

        <NeuCard
          title="兴趣技能热力图"
          subtitle="字号越大，资料中出现的比例越高"
          badge="日期探索"
        >
          <p v-if="selectedTalentItem" class="selection-note">
            {{ selectedTalentItem.name }} · {{ selectedTalentItem.value }}%
          </p>
          <TextHeatCloud
            :items="talents"
            metric="占比"
            unit="%"
            :selected="selectedTalent"
            @select="value => (selectedTalent = value)"
          />
        </NeuCard>
      </div>
    </section>

    <section id="special" class="section">
      <div class="section-head">
        <span class="section-icon"><Activity :size="24" /></span>
        <div>
          <p>藏在数据里的小惊喜</p>
          <h2>我们来自五湖四海，也各有不同</h2>
        </div>
      </div>

      <div class="chart-grid">
        <NeuCard title="民族构成" subtitle="13 个民族，共同组成这一届同学" badge="民族探索">
          <div class="mini-facts">
            <span><strong>19 人</strong>少数民族新生</span>
            <span><strong>5.1%</strong>少数民族占比</span>
            <span><strong>12 个</strong>少数民族类别</span>
            <span><strong>13 个</strong>民族合计</span>
          </div>
          <p v-if="selectedEthnicItem" class="selection-note">
            {{ selectedEthnicItem.name }} ·
            {{ selectedEthnicItem.count === null ? '具体人数未列出' : `${selectedEthnicItem.count} 人` }}，
            {{ selectedEthnicItem.value }}%
          </p>
          <TextHeatCloud
            :items="ethnicGroups"
            metric="占比"
            unit="%"
            :selected="selectedEthnic"
            @select="value => (selectedEthnic = value)"
          />
        </NeuCard>

        <NeuCard title="民族明细" subtitle="少数民族数据呈现人数，汉族呈现比例" badge="民族清单">
          <div class="table-wrap">
            <table>
              <thead>
                <tr>
                  <th>民族</th>
                  <th>人数</th>
                  <th>占比</th>
                </tr>
              </thead>
              <tbody>
                <tr v-for="item in ethnicGroups" :key="item.name">
                  <td>{{ item.name }}</td>
                  <td>{{ item.count === null ? '—' : `${item.count} 人` }}</td>
                  <td>{{ item.value }}%</td>
                </tr>
              </tbody>
            </table>
          </div>
        </NeuCard>
      </div>

      <div class="chart-grid">
        <NeuCard title="来自广东之外" subtitle="跨越 6 个省份与直辖市而来" badge="来源故事">
          <div class="fact-grid">
            <div class="fact-card">
              <strong>{{ nonLocalStudents.outsideGuangdong }}%</strong>
              <span>广东之外的同学</span>
            </div>
            <div class="fact-card">
              <strong>{{ nonLocalStudents.places.length }} 个</strong>
              <span>省份与直辖市来源</span>
            </div>
          </div>
          <div class="chip-row">
            <span v-for="place in nonLocalStudents.places" :key="place">{{ place }}</span>
          </div>
        </NeuCard>

        <NeuCard title="同城相遇" subtitle="来源更集中的细分地区比例" badge="地区分布">
          <EChart :option="detailedRegionOption" height="340px" />
        </NeuCard>
      </div>

      <div class="chart-grid">
        <NeuCard title="星座分布" subtitle="十二星座在这一届的比例" badge="星座地图">
          <EChart :option="zodiacOption" height="380px" />
        </NeuCard>

        <NeuCard title="名字里的多样性" subtitle="姓氏、姓名长度与同名观察" badge="姓名观察">
          <div class="fact-grid compact">
            <div class="fact-card">
              <strong>{{ surnameStructure.categories }}</strong>
              <span>姓氏类别</span>
            </div>
            <div class="fact-card">
              <strong>{{ surnameStructure.singletonCategoryRate }}%</strong>
              <span>只出现一次的姓氏类别</span>
            </div>
            <div class="fact-card">
              <strong>{{ surnameStructure.singletonStudentRate }}%</strong>
              <span>对应同学比例</span>
            </div>
            <div class="fact-card">
              <strong>{{ surnameStructure.sameFullNameRate }}%</strong>
              <span>完全同名比例</span>
            </div>
          </div>
          <DataBars :items="surnames" />
          <div class="divider"></div>
          <DataBars :items="nameLengths" />
        </NeuCard>
      </div>

      <div class="chart-grid">
        <NeuCard title="从哪所高中出发" subtitle="来自 206 所毕业学校，展示来源更集中的学校" badge="高中地图">
          <div class="fact-grid compact">
            <div class="fact-card">
              <strong>{{ schoolStructure.total }}</strong>
              <span>毕业学校数量</span>
            </div>
            <div class="fact-card">
              <strong>{{ schoolStructure.singletonSchoolRate }}%</strong>
              <span>只来了一位同学的学校</span>
            </div>
            <div class="fact-card">
              <strong>{{ schoolStructure.singletonStudentRate }}%</strong>
              <span>来自这些学校的同学</span>
            </div>
          </div>
          <EChart :option="schoolOption" height="350px" />
        </NeuCard>

        <NeuCard title="数据观察维度" subtitle="这份新生画像所呈现的公开数据" badge="观察清单">
          <div class="table-wrap">
            <table>
              <thead>
                <tr>
                  <th>公开维度</th>
                  <th>页面呈现</th>
                </tr>
              </thead>
              <tbody>
                <tr v-for="item in publicDimensions" :key="item.dimension">
                  <td>{{ item.dimension }}</td>
                  <td>{{ item.result }}</td>
                </tr>
              </tbody>
            </table>
          </div>
        </NeuCard>
      </div>

      <NeuCard title="数据来源与参考" subtitle="数据整理自学院资料，观察维度参考高校公开新生数据" badge="资料来源">
        <div class="reference-list">
          <a
            v-for="item in references"
            :key="item.url"
            :href="item.url"
            target="_blank"
            rel="noreferrer"
          >
            <strong>{{ item.org }}</strong>
            <span>{{ item.title }}</span>
            <p>{{ item.dimensions }}</p>
          </a>
        </div>
      </NeuCard>
    </section>

    <footer class="page-footer">
      <p>数据来源：国际教育学院 2026 新生资料（已收集的公开信息）。</p>
      <p>国际教育学院 · 2026 新生画像</p>
    </footer>
  </main>
</template>
