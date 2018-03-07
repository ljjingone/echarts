<template>
  <div id="hello" style="width: 100%;height: 600px;">
    
  </div>
</template>

<script>
import echarts from 'echarts'
export default {
  name: 'HelloWorld',
  data () {
      return {
          charts: '',
          Xmath:[],
          Ymath:[],
          Y1math:[]
          
      }
  },
  methods:{
      drawPie(id){
          var self=this;
          this.charts = echarts.init(document.getElementById(id))
          this.charts.setOption({
            title: {
              text: '实时币价信息',
              subtext: ''
            },
            tooltip: {
              trigger: 'axis'
            },
            legend: {
              data:['卖一价格','买一价格']
            },
            toolbox: {
              show: true,
              feature: {
                dataZoom: {
                  yAxisIndex: 'none'
                },
                dataView: {readOnly: false},
                magicType: {type: ['line', 'bar']},
                restore: {},
                saveAsImage: {}

              }
            },
            
            xAxis:  {
              type: 'category',
              boundaryGap: false,
              data: [],
              axisLabel: {
                interval: 0
                
              }
            },
            yAxis: {
              type: 'value',
              axisLabel: {
                formatter: '{value}CNY '
                
              }
            },
            dataZoom : {  
             show : true,  
             realtime : true,  
             type: 'slider', 
             fillerColor:"rgba(167,183,204,0.4)", 
            borderColor:"#ddd",
             start : 0,  
             end : 30  
            },
            series: [
              {
                name:'卖一价格',
                type:'line',
                data: [],
                markPoint: {
                  data: [
                    {type: 'max', name: '最大值'},
                    {type: 'min', name: '最小值'}
                  ]
                },
                markLine: {
                  data: [
                    {type: 'average', name: '平均值'}
                  ]
                },
                 itemStyle: {
                    normal: {
                        color: "black",
                        
                    }
                }
              },
              {
                name:'买一价格',
                type:'line',
                data: [],
                markPoint: {
                    data: [
                      {type: 'max', name: '最大值'},
                      {type: 'min', name: '最小值'}
                    ]
                  },
                  markLine: {
                    data: [
                      {type: 'average', name: '平均值'}
                    ]
                  }
              }
            ]
      })
      this.$ajax.get('http://127.0.0.1:3001/hangqing').then((res)=>{
        res.data.forEach((item)=>{
          this.Xmath.push(item.name)
          this.Ymath.push(item.buy)
          this.Y1math.push(item.sell)
          
        })
          this.charts.setOption({
            xAxis:{
              data: self.Xmath
            },
            series:[{ data: self.Ymath},{ data: self.Y1math}]
          })
          console.log(this.Xmath)
          console.log(this.Ymath)
          console.log(this.Y1math)
      })
      
        
        
      }
    },
    created(){
      
  
     
    },
  //调用
    mounted(){
        this.$nextTick(function() {
            this.drawPie('hello')
        })
        
            //  console.log(this.option.series[0].data)
    }
    }
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
 * {
        margin: 0;
        padding: 0;
        list-style: none;
    }
h1, h2 {
  font-weight: normal;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
</style>
