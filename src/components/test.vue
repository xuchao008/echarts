<template>
  <div id='container'>
    <div ref='div'></div>
    <div ref="div1"></div>
    <div ref="div2"></div>
  </div>
</template>
<script>
  // 引入Echarts主模块，也可以引入对应的模块
  import echarts from 'echarts'
  export default {
    mounted () {
      // 柱状图
      let myChart = echarts.init(this.$refs.div)
      myChart.setOption({
        title: {text: 'ECharts 入门示例'},
        tooltip: {},
        xAxis: {
          data: ['衬衫', '羊毛衫', '雪纺衫', '裤子', '高跟鞋', '袜子']
        },
        yAxis: {},
        series: [{
          name: '销量',
          type: 'bar',
          data: [5, 20, 36, 10, 10, 20]
        }]
      })
      // 饼图
      let myChart1 = echarts.init(this.$refs.div1)
      myChart1.setOption({
        series: [
          {
            name: '访问来源',
            type: 'pie',
            radius: '55%',
            roseType: 'angel',
            data: [
              {value: 235, name: '视频广告'},
              {value: 274, name: '联盟广告'},
              {value: 310, name: '邮件营销'},
              {value: 335, name: '直接访问'},
              {value: 400, name: '搜索引擎'}
            ]
          }
        ]
      })
      // 折线图
      let myChart2 = echarts.init(this.$refs.div2)

      // 定义拖拽
      var symbolSize = 21
      var data = [[15, 0], [-50, 10], [-56.5, 20], [-46.5, 30], [-22.1, 40]]
      var app = {}
      var option = {
        title: {
          text: 'Try Dragging these Points'
        },
        tooltip: {
          formatter: function (params) {
            return 'X: ' + params.data[0].toFixed(2) + '<br>Y: ' + params.data[1].toFixed(2)
          }
        },
        grid: {
        },
        xAxis: {
          min: -100,
          max: 80,
          type: 'value',
          axisLine: {onZero: false}
        },
        yAxis: {
          min: -30,
          max: 60,
          type: 'value',
          axisLine: {onZero: false}
        },
        dataZoom: [
          {
            type: 'slider',
            xAxisIndex: 0,
            filterMode: 'empty'
          },
          {
            type: 'slider',
            yAxisIndex: 0,
            filterMode: 'empty'
          },
          {
            type: 'inside',
            xAxisIndex: 0,
            filterMode: 'empty'
          },
          {
            type: 'inside',
            yAxisIndex: 0,
            filterMode: 'empty'
          }
        ],
        series: [
          {
            id: 'a',
            type: 'line',
            smooth: true,
            symbolSize: symbolSize,
            data: data
          }
        ]
      }
      myChart2.setOption(option, true)
      if (!app.inNode) {
        setTimeout(function () {
          // Add shadow circles (which is not visible) to enable drag.
          myChart2.setOption({
            graphic: echarts.util.map(data, function (item, dataIndex) {
              return {
                type: 'circle',
                position: myChart.convertToPixel('grid', item),
                shape: {
                  cx: 0,
                  cy: 0,
                  r: symbolSize / 2
                },
                invisible: true,
                draggable: true,
                ondrag: echarts.util.curry(onPointDragging, dataIndex),
                onmousemove: echarts.util.curry(showTooltip, dataIndex),
                onmouseout: echarts.util.curry(hideTooltip, dataIndex),
                z: 100
              }
            })
          })
        }, 0)

        window.addEventListener('resize', updatePosition)
      }

      myChart2.on('dataZoom', updatePosition)

      function updatePosition () {
        myChart2.setOption({
          graphic: echarts.util.map(data, function (item, dataIndex) {
            return {
              position: myChart2.convertToPixel('grid', item)
            }
          })
        })
      }

      function showTooltip (dataIndex) {
        myChart.dispatchAction({
          type: 'showTip',
          seriesIndex: 0,
          dataIndex: dataIndex
        })
      }

      function hideTooltip (dataIndex) {
        myChart2.dispatchAction({
          type: 'hideTip'
        })
      }

      function onPointDragging (dataIndex, dx, dy) {
        data[dataIndex] = myChart2.convertFromPixel('grid', this.position)

        // Update data
        myChart2.setOption({
          series: [{
            id: 'a',
            data: data
          }]
        })
      }
    }
  }
</script>

<style>
  #container div {
    height: 400px;
  }
</style>
