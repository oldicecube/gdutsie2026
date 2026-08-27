<script setup>
import { computed, ref } from 'vue'
import {
  Activity,
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
    <section id="overview" class="section">
      <div class="section-head">
        <span class="section-icon"><BarChart3 :size="24" /></span>
        <div>
          <p>数据口径</p>
          <h2>当前“部分”新生数据</h2>
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
          <p>基础画像</p>
          <h2>性别、政治面貌与来源地</h2>
        </div>
      </div>

      <div class="chart-grid">
        <NeuCard title="性别" subtitle="常规人群指标仅呈现比例" badge="环形图">
          <EChart :option="genderOption" height="320px" />
        </NeuCard>

        <NeuCard title="政治面貌" subtitle="常规人群指标仅呈现比例" badge="玫瑰图">
          <EChart :option="politicalOption" height="320px" />
        </NeuCard>

        <NeuCard title="省份来源" subtitle="广东占比最高" badge="条形图">
          <EChart :option="provinceOption" height="330px" />
        </NeuCard>

        <NeuCard title="考生地区" subtitle="按城市/区域汇总，居前部分" badge="条形图">
          <EChart :option="regionOption" height="330px" />
        </NeuCard>

        <NeuCard title="考生类别" subtitle="常规人群指标仅呈现比例" badge="条形图">
          <EChart :option="candidateOption" height="300px" />
        </NeuCard>
      </div>
    </section>

    <section id="programs" class="section">
      <div class="section-head">
        <span class="section-icon"><GraduationCap :size="24" /></span>
        <div>
          <p>录取专业项目</p>
          <h2>专业项目分布</h2>
        </div>
      </div>

      <NeuCard title="录取专业项目" subtitle="9 个专业项目占比" badge="条形图">
        <EChart :option="programOption" height="430px" />
      </NeuCard>
    </section>

    <section id="birthdays" class="section">
      <div class="section-head">
        <span class="section-icon"><Cake :size="24" /></span>
        <div>
          <p>生日数据</p>
          <h2>出生年份、月份与重复生日</h2>
        </div>
      </div>

      <div class="chart-grid">
        <NeuCard title="出生年份" subtitle="仅保留 2007、2008" badge="环形图">
          <EChart :option="birthYearOption" height="320px" />
        </NeuCard>

        <NeuCard title="出生月份" subtitle="全部 12 个月" badge="面积图">
          <EChart :option="birthMonthOption" height="320px" />
        </NeuCard>
      </div>

      <NeuCard
        title="重复生日日期"
        subtitle="78 个生日日期至少重复 2 次；字号随重复人数增大"
        badge="文字热力图"
      >
        <p v-if="selectedBirthdayItem" class="selection-note">
          当前选中：{{ selectedBirthdayItem.name }}，{{ selectedBirthdayItem.value }} 人
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
        <NeuCard title="重复生日明细" subtitle="按同一日期重复人数分组" badge="数据表">
          <div class="table-wrap">
            <table>
              <thead>
                <tr>
                  <th>重复人数</th>
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

        <NeuCard title="特殊日期生日" subtitle="有生日记录的节日/纪念日" badge="数据表">
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
          <p>新生特长</p>
          <h2>特长标签与占比</h2>
        </div>
      </div>

      <div class="chart-grid">
        <NeuCard title="特长标签" subtitle="同一人可同时命中多个标签" badge="雷达图">
          <EChart :option="talentRadarOption" height="420px" />
        </NeuCard>

        <NeuCard
          title="特长文字热力图"
          subtitle="字号越大，占比越高"
          badge="文字热力图"
        >
          <p v-if="selectedTalentItem" class="selection-note">
            当前选中：{{ selectedTalentItem.name }}，{{ selectedTalentItem.value }}%
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
          <p>小众特色数据</p>
          <h2>民族、外省来源与补充数据</h2>
        </div>
      </div>

      <div class="chart-grid">
        <NeuCard title="民族构成" subtitle="少数民族类别与占比" badge="文字热力图">
          <div class="mini-facts">
            <span><strong>19 人</strong>少数民族新生</span>
            <span><strong>5.1%</strong>少数民族占比</span>
            <span><strong>12 个</strong>少数民族类别</span>
            <span><strong>13 个</strong>民族合计</span>
          </div>
          <p v-if="selectedEthnicItem" class="selection-note">
            当前选中：{{ selectedEthnicItem.name }}，
            {{ selectedEthnicItem.count === null ? '人数不展示' : `${selectedEthnicItem.count} 人` }}，
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

        <NeuCard title="民族明细" subtitle="少数民族保留具体人数，汉族仅保留比例" badge="数据表">
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
        <NeuCard title="外省生源" subtitle="广东以外省份来源" badge="数据卡">
          <div class="fact-grid">
            <div class="fact-card">
              <strong>{{ nonLocalStudents.outsideGuangdong }}%</strong>
              <span>广东以外新生占比</span>
            </div>
            <div class="fact-card">
              <strong>{{ nonLocalStudents.places.length }} 个</strong>
              <span>外省/直辖市来源</span>
            </div>
          </div>
          <div class="chip-row">
            <span v-for="place in nonLocalStudents.places" :key="place">{{ place }}</span>
          </div>
        </NeuCard>

        <NeuCard title="最集中的细分考生地区" subtitle="考生地区是原始字段" badge="条形图">
          <EChart :option="detailedRegionOption" height="340px" />
        </NeuCard>
      </div>

      <div class="chart-grid">
        <NeuCard title="星座分布" subtitle="全部 12 个星座" badge="条形图">
          <EChart :option="zodiacOption" height="380px" />
        </NeuCard>

        <NeuCard title="姓氏与姓名结构" subtitle="姓氏类别与文本长度" badge="数据卡 + 条形图">
          <div class="fact-grid compact">
            <div class="fact-card">
              <strong>{{ surnameStructure.categories }}</strong>
              <span>姓氏类别</span>
            </div>
            <div class="fact-card">
              <strong>{{ surnameStructure.singletonCategoryRate }}%</strong>
              <span>仅出现一次的姓氏类别占比</span>
            </div>
            <div class="fact-card">
              <strong>{{ surnameStructure.singletonStudentRate }}%</strong>
              <span>对应新生占比</span>
            </div>
            <div class="fact-card">
              <strong>{{ surnameStructure.sameFullNameRate }}%</strong>
              <span>完全同名新生占比</span>
            </div>
          </div>
          <DataBars :items="surnames" />
          <div class="divider"></div>
          <DataBars :items="nameLengths" />
        </NeuCard>
      </div>

      <div class="chart-grid">
        <NeuCard title="毕业学校来源分布" subtitle="来源学校数量与居前学校" badge="条形图">
          <div class="fact-grid compact">
            <div class="fact-card">
              <strong>{{ schoolStructure.total }}</strong>
              <span>毕业学校名称类别</span>
            </div>
            <div class="fact-card">
              <strong>{{ schoolStructure.singletonSchoolRate }}%</strong>
              <span>单一新生来源学校占比</span>
            </div>
            <div class="fact-card">
              <strong>{{ schoolStructure.singletonStudentRate }}%</strong>
              <span>这部分学校来源新生占比</span>
            </div>
          </div>
          <EChart :option="schoolOption" height="350px" />
        </NeuCard>

        <NeuCard title="同类高校公开新生数据中的已使用维度" subtitle="当前数据对应结果" badge="数据表">
          <div class="table-wrap">
            <table>
              <thead>
                <tr>
                  <th>公开维度</th>
                  <th>当前数据对应结果</th>
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

      <NeuCard title="同类高校公开推文参考来源" subtitle="用于数据维度参考" badge="参考来源">
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
      <p>数据来源：当前 Excel 文件中的“部分”新生记录。</p>
      <p>静态展示页面 · Vue 3 + Vite + ECharts</p>
    </footer>
  </main>
</template>

