<template>
  <div :class="className" :style="{height:height,width:width}" />
</template>

<script>
import echarts from 'echarts'
require('echarts/theme/macarons') // echarts theme
import resize from './mixins/resize'

export default {
  mixins: [resize],
  props: {
    className: {
      type: String,
      default: 'chart'
    },
    width: {
      type: String,
      default: '100%'
    },
    height: {
      type: String,
      default: '400px'
    },
    tokens: {
      type: Object,
      required: true
    },
    links: {
      type: Object,
      required: true
    }
  },
  data() {
    return {
      chart: null
    }
  },
  computed: {
    listenChange() {
      const { tokens, links } = this
      return { tokens, links }
    }
  },
  watch: {
    listenChange: {
      deep: true,
      handler(val) {
        this.setOptions(val.tokens, val.links)
      }
    }
  },
  mounted() {
    this.$nextTick(() => {
      this.initChart()
    })
  },
  beforeDestroy() {
    if (!this.chart) {
      return
    }
    this.chart.dispose()
    this.chart = null
  },
  methods: {
    initChart() {
      this.chart = echarts.init(this.$el, 'macarons')
      this.setOptions(this.tokens, this.links)
    },
    setOptions(tokens, links) {
      this.chart.setOption({
        // color: ['#67001f', '#b2182b', '#d6604d', '#f4a582', '#fddbc7', '#d1e5f0', '#92c5de', '#4393c3', '#2166ac', '#053061'],
        tooltip: {
          trigger: 'item',
          triggerOn: 'mousemove'
        },
        animation: false,
        series: [
          {
            type: 'sankey',
            bottom: '10%',
            left: '20%',
            focusNodeAdjacency: 'allEdges',
            data: tokens,
            links: links,
            // orient: 'vertical',
            label: {
              // position: 'top'
            },
            lineStyle: {
              color: 'source',
              curveness: 0.5
            }
          }
        ]
      })
      this.chart.on('click', (params) => {
        // window.open('https://www.baidu.com/s?wd=' + encodeURIComponent(params.name))
        this.$emit('func', params.name)
      })
    }
  }
}
</script>
