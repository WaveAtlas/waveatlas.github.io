<template>
  <q-page class="dashboard-page">
    <div class="dashboard-page__inner">
      <header class="dashboard-header">
        <div>
          <h1 class="dashboard-header__title">Analytics Dashboard</h1>
          <p class="dashboard-header__subtitle">Interactive chart and filter form</p>
        </div>
        <q-badge class="dashboard-header__badge" label="Live View" />
      </header>

      <div class="row q-col-gutter-lg dashboard-grid">
        <div class="col-12 col-md-8">
          <q-card flat bordered class="dashboard-card">
            <q-card-section class="dashboard-card__header">
              <span class="dashboard-card__label">Performance Chart</span>
            </q-card-section>
            <q-separator />
            <q-card-section class="chart-section">
              <v-chart class="performance-chart" :option="chartOption" autoresize />
            </q-card-section>
          </q-card>
        </div>

        <div class="col-12 col-md-4">
          <q-card flat bordered class="dashboard-card dashboard-card--filters">
            <q-card-section class="dashboard-card__header">
              <span class="dashboard-card__label">Filters</span>
            </q-card-section>
            <q-separator />
            <q-card-section>
              <q-form class="filters-form" @submit.prevent="onApply">
                <q-input
                  v-model="filters.name"
                  outlined
                  dense
                  placeholder="Name"
                  class="filters-form__field"
                />
                <q-file
                  v-model="filters.file"
                  outlined
                  dense
                  label="Upload File"
                  class="filters-form__field"
                  clearable
                />
                <q-select
                  v-model="filters.category"
                  :options="categoryOptions"
                  outlined
                  dense
                  label="Category"
                  class="filters-form__field"
                />
                <q-checkbox
                  v-model="filters.acceptTerms"
                  label="Accept terms"
                  class="filters-form__checkbox"
                />
                <div class="filters-form__radio-group">
                  <div class="filters-form__radio-label">Chart Type</div>
                  <q-option-group
                    v-model="filters.chartType"
                    :options="chartTypeOptions"
                    type="radio"
                    inline
                    color="primary"
                  />
                </div>
                <div class="filters-form__actions">
                  <q-btn unelevated color="primary" label="APPLY" type="submit" class="filters-form__apply" />
                  <q-btn flat color="grey-8" label="RESET" type="button" @click="onReset" />
                </div>
              </q-form>
            </q-card-section>
          </q-card>
        </div>
      </div>
    </div>
  </q-page>
</template>

<script setup>
import { computed, ref } from 'vue'
import { use } from 'echarts/core'
import { CanvasRenderer } from 'echarts/renderers'
import { LineChart } from 'echarts/charts'
import {
  GridComponent,
  LegendComponent,
  TitleComponent,
  ToolboxComponent,
  TooltipComponent,
} from 'echarts/components'
import VChart from 'vue-echarts'

use([
  CanvasRenderer,
  LineChart,
  TitleComponent,
  TooltipComponent,
  LegendComponent,
  GridComponent,
  ToolboxComponent,
])

const SERIES_A = [10, 15, 13, 17]
const SERIES_B = [16, 5, 11, 9]
const PERIODS = [1, 2, 3, 4]

const defaultFilters = () => ({
  name: '',
  file: null,
  category: 'Option 1',
  acceptTerms: false,
  chartType: 'line',
})

const filters = ref(defaultFilters())
const chartTypeApplied = ref('line')

const categoryOptions = ['Option 1', 'Option 2', 'Option 3']
const chartTypeOptions = [
  { label: 'Line', value: 'line' },
  { label: 'Smooth', value: 'smooth' },
]

const chartOption = computed(() => {
  const smooth = chartTypeApplied.value === 'smooth'

  return {
    title: {
      text: 'Quarterly Trend',
      left: 0,
      top: 0,
      textStyle: {
        fontSize: 14,
        fontWeight: 600,
        color: '#1a1a1a',
      },
    },
    color: ['#1976d2', '#26a69a'],
    tooltip: {
      trigger: 'axis',
    },
    legend: {
      data: ['Series A', 'Series B'],
      bottom: 0,
      left: 0,
    },
    grid: {
      left: 48,
      right: 24,
      top: 56,
      bottom: 48,
    },
    toolbox: {
      right: 8,
      top: 8,
      feature: {
        saveAsImage: {},
        dataView: { readOnly: true },
        restore: {},
        dataZoom: {},
      },
      iconStyle: {
        borderColor: '#9e9e9e',
      },
    },
    xAxis: {
      type: 'value',
      name: 'Period',
      nameLocation: 'middle',
      nameGap: 28,
      min: 1,
      max: 4,
      interval: 0.5,
      splitLine: {
        lineStyle: { color: '#eeeeee' },
      },
      axisLine: { lineStyle: { color: '#bdbdbd' } },
      axisLabel: { color: '#757575' },
    },
    yAxis: {
      type: 'value',
      name: 'Value',
      min: 4,
      max: 18,
      interval: 2,
      splitLine: {
        lineStyle: { color: '#eeeeee' },
      },
      axisLine: { lineStyle: { color: '#bdbdbd' } },
      axisLabel: { color: '#757575' },
    },
    series: [
      {
        name: 'Series A',
        type: 'line',
        smooth,
        symbol: 'circle',
        symbolSize: 8,
        data: PERIODS.map((x, i) => [x, SERIES_A[i]]),
      },
      {
        name: 'Series B',
        type: 'line',
        smooth,
        symbol: 'circle',
        symbolSize: 8,
        data: PERIODS.map((x, i) => [x, SERIES_B[i]]),
      },
    ],
  }
})

function onApply() {
  chartTypeApplied.value = filters.value.chartType
}

function onReset() {
  filters.value = defaultFilters()
  chartTypeApplied.value = 'line'
}
</script>

<style scoped>
.dashboard-page {
  background: #f4f5f7;
  min-height: 100vh;
}

.dashboard-page__inner {
  max-width: 1200px;
  margin: 0 auto;
  padding: 32px 24px 48px;
}

.dashboard-header {
  display: flex;
  align-items: flex-start;
  justify-content: space-between;
  margin-bottom: 24px;
}

.dashboard-header__title {
  margin: 0;
  font-size: 1.75rem;
  font-weight: 700;
  color: #1a1a1a;
  line-height: 1.2;
}

.dashboard-header__subtitle {
  margin: 6px 0 0;
  font-size: 0.95rem;
  color: #757575;
}

.dashboard-header__badge {
  background: #e3f2fd;
  color: #1976d2;
  font-weight: 600;
  font-size: 0.75rem;
  padding: 6px 12px;
  border-radius: 6px;
}

.dashboard-card {
  border-radius: 12px;
  background: #fff;
  border-color: #e8eaed;
}

.dashboard-card__header {
  padding: 16px 20px;
}

.dashboard-card__label {
  font-size: 0.95rem;
  font-weight: 600;
  color: #424242;
}

.chart-section {
  padding: 8px 12px 16px;
}

.performance-chart {
  width: 100%;
  height: 380px;
}

.dashboard-card--filters {
  height: 100%;
}

.filters-form__field {
  margin-bottom: 16px;
}

.filters-form__checkbox {
  margin-bottom: 20px;
}

.filters-form__radio-group {
  margin-bottom: 24px;
}

.filters-form__radio-label {
  font-size: 0.875rem;
  color: rgba(0, 0, 0, 0.6);
  margin-bottom: 8px;
}

.filters-form__actions {
  display: flex;
  align-items: center;
  justify-content: space-between;
  gap: 12px;
}

.filters-form__apply {
  min-width: 100px;
  font-weight: 600;
  letter-spacing: 0.04em;
}
</style>
