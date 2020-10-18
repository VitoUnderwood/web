<template>
  <div class="app-container">
    <!-- <a class="x-btn" target="_blank" href="https://www.baidu.com">hello world</a> -->
    <el-input
      v-model="textarea"
      type="textarea"
      :autosize="{ minRows: 3, maxRows: 6 }"
      placeholder="金牛座人和巨蟹座人一样，都属于思想悲观的类型。看问题总喜欢抓着不好的一面，然后扩大解读其中的含义。如此一来，便导致了金牛座人常常被负面情绪支配着，随时随地都在抱怨着、各种唠叨、各种牢骚。自以为发泄坏情绪可以减轻压力，但他们从没想过，自己的压力倒是减轻了，却给别人徒增了不少烦恼！"
    />
    <el-button
      style="margin-top: 20px"
      type="primary"
      icon="el-icon-document"
      @click="post_data"
    >
      预测主题分布
    </el-button>
    <!-- <el-button v-for="item in tokens" :key="item.id" type="text">{{ item }}</el-button> -->
    <el-row style="background: #fff; padding: 16px 16px 0; margin-bottom: 32px">
      <el-row :gutter="32">
        <el-col :xs="24" :sm="24" :lg="24">
          <div class="chart-wrapper">
            <word-cloud-chart :data="keywords" />
          </div></el-col>
      </el-row>
    </el-row>

    <el-row v-if="tokens.length>0" style="background: #fff; padding: 16px 16px 0; margin-bottom: 32px">
      <el-row :gutter="32">
        <el-col :xs="24" :sm="24" :lg="24">
          <div class="chart-wrapper">
            <sanky-chart :tokens="tokens" :links="links" @func="getMsgFormSon" />
          </div>
        </el-col>
      </el-row>
    </el-row>

    <el-row v-if="show" style="background: #fff; padding: 16px 16px 0; margin-bottom: 32px">
      <el-row :gutter="32">
        <el-col :xs="24" :sm="24" :lg="24">
          <div class="chart-wrapper">
            <sanky-chart :tokens="tokens_2" :links="links_2" />
          </div>
        </el-col>
      </el-row>
    </el-row>

    <el-row v-if="pieChartData.length>0" style="background: #fff; padding: 16px 16px 0; margin-bottom: 32px">
      <el-row :gutter="32">
        <el-col :xs="24" :sm="24" :lg="24">
          <div class="chart-wrapper">
            <pie-chart :chart-data="pieChartData" />
          </div>
        </el-col>
      </el-row>
    </el-row>
  </div>
</template>

<script>
import axios from 'axios'
import PieChart from './components/PieChart'
import SankyChart from './components/SankyChart'
import WordCloudChart from './components/WordCloudChart'
require('echarts/theme/macarons') // echarts theme
require('echarts-wordcloud')

// const pieChartData = [
//   { value: 0.06822415161877871, name: 'finance' },
//   { value: 0.03144104266539216, name: 'realty' },
//   { value: 0.04129994777031243, name: 'stocks' },
//   { value: 0.021919746359344572, name: 'Gold' },
//   { value: 0.13376405695453286, name: 'science' },
//   { value: 0.12403191067278385, name: 'society' },
//   { value: 0.12982344487681985, name: 'politics' },
//   { value: 3.941512480378151, name: 'sports' },
//   { value: 0.037755025550723076, name: 'game' },
//   { value: 95.47022581100464, name: 'entertainment' }
// ]

export default {
  components: {
    PieChart,
    SankyChart,
    WordCloudChart
  },
  data() {
    return {
      keywords: [
        {
          name: '十九大精神',
          value: 15000
        },
        {
          name: '两学一做',
          value: 10081
        },
        {
          name: '中华民族',
          value: 9386
        },
        {
          name: '马克思主义',
          value: 7500
        },
        {
          name: '民族复兴',
          value: 7500
        },
        {
          name: '社会主义制度',
          value: 6500
        },
        {
          name: '国防白皮书',
          value: 6500
        },
        {
          name: '创新',
          value: 6000
        },
        {
          name: '民主革命',
          value: 4500
        },
        {
          name: '文化强国',
          value: 3800
        },
        {
          name: '国家主权',
          value: 3000
        },
        {
          name: '武装颠覆',
          value: 2500
        },
        {
          name: '领土完整',
          value: 2300
        },
        {
          name: '安全',
          value: 2000
        },
        {
          name: '从严治党',
          value: 1900
        },
        {
          name: '现代化经济体系',
          value: 1800
        },
        {
          name: '国防动员',
          value: 1700
        },
        {
          name: '信息化战争',
          value: 1600
        },
        {
          name: '局部战争',
          value: 1500
        },
        {
          name: '教育',
          value: 1200
        },
        {
          name: '职业教育',
          value: 1100
        },
        {
          name: '兵法',
          value: 900
        },
        {
          name: '一国两制',
          value: 800
        },
        {
          name: '特色社会主义思想',
          value: 700
        }
      ],
      textarea: '金牛座人和巨蟹座人一样，都属于思想悲观的类型。看问题总喜欢抓着不好的一面，然后扩大解读其中的含义。如此一来，便导致了金牛座人常常被负面情绪支配着，随时随地都在抱怨着、各种唠叨、各种牢骚。自以为发泄坏情绪可以减轻压力，但他们从没想过，自己的压力倒是减轻了，却给别人徒增了不少烦恼！',
      show: false,
      pieChartData: [],
      tokens: [],
      tokens_2: [],
      links_2: [],
      links: []
    }
  },

  mounted() {
  },

  methods: {
    getMsgFormSon(data) {
      // 请求后台获取对应主题的分布情况
      axios
        .post('http://localhost:5000/get_topic_dis', {
          topic_id: data
        })
        .then((response) => {
          this.show = true
          console.log(response.data)
          this.tokens_2 = response.data['tokens']
          this.links_2 = response.data['links']
        })
        .catch((error) => {
          console.log(error)
          this.show = false
        })
    },
    get_data: function() {
      axios
        .get('http://localhost:5000/test')
        .then(function(response) {
          console.log(response.data)
        })
        .catch(function(error) {
          console.log(error)
        })
    },
    post_data: function() {
      axios
        .post('http://localhost:5000/login', {
          text: this.textarea
        })
        .then((response) => {
          console.log(response.data)
          this.pieChartData = response.data['pieChartData']
          this.tokens = response.data['tokens']
          this.links = response.data['links']
          this.keywords = response.data['keywords']
        })
        .catch(function(error) {
          console.log(error)
        })
    }
  }
}
</script>

<style lang="scss" scoped>
</style>
