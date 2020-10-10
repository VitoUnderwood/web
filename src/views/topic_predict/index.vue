<template>
  <div class="app-container">
    <!-- <a class="x-btn" target="_blank" href="https://www.baidu.com">hello world</a> -->
    <el-input
      v-model="textarea1"
      type="textarea"
      :autosize="{ minRows: 3, maxRows: 6}"
      placeholder="燕京啤酒8日晚间发布重大事项公告，公司董事长、总经理赵晓东因涉嫌职务违法，近日被有关部门立案调查并采取留置措施，不能正常履职。公司工作暂由副董事长、常务副总经理谢广军主持，各项经营活动正常开展。"
    />
    <el-button style="margin-top:80px;" type="primary" icon="el-icon-document" @click="post_data">
      预测主题分布
    </el-button>
    <el-row style="background:#fff;padding:16px 16px 0;margin-bottom:32px;">
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

const pieChartData = [
  { value: 0.06822415161877871, name: 'finance' },
  { value: 0.03144104266539216, name: 'realty' },
  { value: 0.04129994777031243, name: 'stocks' },
  { value: 0.021919746359344572, name: 'Gold' },
  { value: 0.13376405695453286, name: 'science' },
  { value: 0.12403191067278385, name: 'society' },
  { value: 0.12982344487681985, name: 'politics' },
  { value: 3.941512480378151, name: 'sports' },
  { value: 0.037755025550723076, name: 'game' },
  { value: 95.47022581100464, name: 'entertainment' }
]

export default {
  components: {
    PieChart
  },
  data() {
    return {
      textarea1: '',
      pieChartData: pieChartData
    }
  },

  methods: {
    get_data: function() {
      axios.get('http://localhost:5000/test')
        .then(function(response) {
          console.log(response.data)
        })
        .catch(function(error) {
          console.log(error)
        })
    },
    post_data: function() {
      axios.post('http://192.168.140.21:5000/login', {
        news: this.textarea1
      })
        .then(response => {
          console.log(response.data['data'])
          this.pieChartData = response.data['data']
        })
        .catch(function(error) {
          console.log(error)
        })
    }
  }
}
</script>

<style lang="scss" scoped>
.x-btn {
  flex-shrink: 0;
  display: block;
  cursor: pointer;
  background: black;
  color: white;
  height: 60px;
  padding: 0 16px;
  margin: 16px;
  line-height: 60px;
  font-size: 20px;
  text-align: center;
}
</style>
