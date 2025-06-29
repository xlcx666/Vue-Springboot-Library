<template>
  <div style="position: relative; height: calc(100vh - 50px); overflow: auto;width: calc(100vw - 200px);">
    <div style="position: absolute;width: 100%">
      <el-card style="margin: 20px;padding-top: 20px;">
        <h1 style="margin: 0 15px 35px 15px;">欢迎使用图书管理系统</h1>
        <el-row :gutter="20">
          <el-col :span="6" v-for="item in cards" :key="item.title">
            <el-card class="box-card">
              <div slot="header" class="clearfix">{{ item.title }}</div>
              <div class="text item">
                <svg class="icon" aria-hidden="true">
                  <use :xlink:href="item.icon" style="width: 100px"></use>
                </svg>
                <span class="text">{{ item.data }}</span>
              </div>
            </el-card>
          </el-col>
        </el-row>
        <!-- 为 ECharts 准备一个具备大小（宽高）的 DOM -->
        <div id="main" style="margin-left: 5px"></div>
      </el-card>
    </div>


  </div>
</template>

<script>
import * as echarts from 'echarts'
import {ElMessage} from "element-plus";
import request from "../utils/request";

export default {
  data() {
    return {
      cards: [
        {title: '已借阅', data: 100, icon: '#iconlend-record-pro'},
        {title: '总访问', data: 100, icon: '#iconvisit'},
        {title: '图书数', data: 100, icon: '#iconbook-pro'},
        {title: '用户数', data: 1000, icon: '#iconpopulation'}
      ],
      data: {}
    }
  },
  created() {

  },
  mounted() {
    request.get("/dashboard").then(res => {
      if (res.code == 0) {

        this.cards[0].data = res.data.lendRecordCount
        this.cards[1].data = res.data.visitCount
        this.cards[2].data = res.data.bookCount
        this.cards[3].data = res.data.userCount

      } else {
        ElMessage.error(res.msg)
      }


      // 基于准备好的dom，初始化echarts实例
      var myChart = echarts.init(document.getElementById('main'))
      console.log(this.cards[0].data)
      // 绘制图表
      myChart.setOption({
        title: {
          text: '统计'
        },
        tooltip: {
          trigger: 'axis'
          // axisPointer: {
          //   type: 'shadow' // 默认为直线，可选为：'line' | 'shadow'
          // }
        },
        grid: {
          left: '3%',
          right: '4%',
          bottom: '3%',
          containLabel: true
        },
        xAxis: {
          type: 'category',
          data: this.cards.map(item => item.title),
          axisTick: {
            alignWithLabel: true
          }
        },
        yAxis: {
          type: 'value'
        },
        series: [
          {
            type: 'bar',
            label: {show: true},
            barWidth: '25%',
            data: [
              {
                value: this.cards[0].data,
                itemStyle: {color: '#5470c6'}
              },
              {
                value: this.cards[1].data,
                itemStyle: {color: '#91cc75'}
              },
              {
                value: this.cards[2].data,
                itemStyle: {color: '#fac858'}
              },
              {
                value: this.cards[3].data,
                itemStyle: {color: '#ee6666'}
              }
            ]
          }
        ]
      })
      window.addEventListener('resize', () => {
        myChart.resize()
      })
    })
  },
  methods: {}
}
</script>

<style scoped>
.box-card {
  width: 80%;
  margin-bottom: 25px;
  margin-left: 10px;
}

.clearfix {
  text-align: center;
  font-size: 15px;
}

.text {
  text-align: center;
  font-size: 24px;
  font-weight: 700;
  vertical-align: super;
}

#main {
  width: 100%;
  height: 450px;
  margin-top: 20px;
}

.icon {
  width: 50px;
  height: 50px;
  padding-top: 5px;
  padding-right: 10px;
}
</style>
