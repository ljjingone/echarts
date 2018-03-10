<template>
<div>
	
	<!-- <button @click="start() ">start</button>
	<button @click="stop()">stop</button> -->
	<div style="margin-top:30px;">
		<el-button @click="start()"  type="primary" >开始</el-button>
		<el-button @click="stop()" type="success">停止</el-button>
	</div>
	<!-- <div>
		<el-table
		:data="tableData"
		style="width: 100%;border:1px solid #000;margin:20px 0;">
		<el-table-column
			prop="date"
			label="平台"
			>
		</el-table-column>
		<el-table-column
			prop="name"
			label="余额">
		</el-table-column>
		<el-table-column
			prop="address"
			label="使用金钱">
		</el-table-column>
		<el-table-column
			prop="address"
			label="eth数量">
		</el-table-column>
		<el-table-column
			prop="address"
			label="btc数量">
		</el-table-column>
		</el-table>
	</div> -->
	<div style="margin:20px 0;">
		<table class="hovertable" >
			<tr>
				<th>平台</th><th>余额</th><th>使用金钱</th><th>eth数量</th><th>btc数量</th>
			</tr>
			<tr v-for="(item,key) in tableData" :key="key" onmouseover="this.style.backgroundColor='#ffff66';" onmouseout="this.style.backgroundColor='#d4e3e5';">
				<td>{{ item.date }} + {{key}}</td><td>{{item.name}}</td><td>{{item.date}}</td><td>{{item.date}}</td><td>{{item.date}}</td>
			</tr>
			
		</table>
	</div>
	<div id="hello" style="width: 100%;height: 600px;">
		
	</div>
  
</div>
</template>

<script>
import echarts from "echarts";
export default {
  name: "HelloWorld",
  data() {
    return {
      charts: "",
      Xmath: [],
      Ymath: [],
	  Y1math: [],
	  tableData: [{
            date: 'huobi',
            name: '3',
            address: '2'
          }, {
            date: 'huobi',
            name: '4',
            address: '6'
          }, {
            date: 'huobi',
            name: '7',
            address: '7'
          }, {
            date: 'huobi',
            name: '8',
            address: '9'
          }]
    };
  },
  methods: {
    drawPie(id, data) {
      var self = this;
      this.charts = echarts.init(document.getElementById(id));
      this.charts.setOption({
        title: {
          text: "实时币价信息",
          subtext: ""
        },
        tooltip: {
          trigger: "axis"
        },
        animation: false,
        legend: {
          data: ["卖一价格", "买一价格"]
        },
        toolbox: {
          show: true,
          feature: {
            dataZoom: {
              yAxisIndex: "none"
            },
            dataView: { readOnly: false },
            magicType: { type: ["line", "bar"] },
            restore: {},
            saveAsImage: {}
          }
        },

        xAxis: {
          type: "category",
          boundaryGap: false,
          data: [],
          axisLabel: {
            interval: 0
          }
        },
        yAxis: {
          type: "value",
          axisLabel: {
            formatter: "{value}CNY "
          }
        },
        // dataZoom: {
        //   show: true,
        //   realtime: true,
        //   type: "slider",
        //   fillerColor: "rgba(167,183,204,0.4)",
        //   borderColor: "#ddd",
        //   start: 0,
        //   end: 30
        // },
        dataZoom: [
          {
            type: "slider",
            show: true,
            xAxisIndex: [0],
            start: 1,
            end: 35
          },
          {
            type: "slider",
            show: true,
            yAxisIndex: [0],
            left: "93%",
            start: 0,
            end: 100
          },
          {
            type: "inside",
            xAxisIndex: [0],
            start: 1,
            end: 35
          }
          // {
          // 	type: 'inside',
          // 	yAxisIndex: [0],
          // 	start: 0,
          // 	end: 100
          // }
        ],
        series: [
          {
            name: "卖一价格",
            type: "line",
            stack: "总量",
            areaStyle: { normal: {} },
            label: {
              normal: {
                show: true,
                position: "top"
              }
            },
            data: [],
            // markPoint: {
            // 	data: [
            // 		{ type: "max", name: "最大值" },
            // 		{ type: "min", name: "最小值" }
            // 	]
            // },
            markLine: {
              data: [{ type: "average", name: "平均值" }]
            },
            itemStyle: {
              normal: {
                color: "red"
              }
            }
          },
          {
            name: "买一价格",
            type: "line",
            areaStyle: {
              normal: {
                color: "#eee"
              }
            },
            data: [],
            label: {
              normal: {
                show: true,
                position: "bottom"
              }
            },
            // markPoint: {
            // 	data: [
            // 		{ type: "max", name: "最大值" },
            // 		{ type: "min", name: "最小值" }
            // 	]
            // },
            markLine: {
              data: [{ type: "average", name: "平均值" }]
            },
            // symbol: 'triangle',
            // symbolSize: 20,
            itemStyle: {
              normal: {
                color: "green"
              }
            }
          }
        ]
      });
      this.$ajax.get("http://127.0.0.1:3001/hangqing").then(res => {
        this.Xmath = [];
        this.Y1math = [];
        this.Ymath = [];
        res.data.forEach(item => {
          this.Xmath.push(item.name);
          this.Ymath.push(item.buy);
          this.Y1math.push(item.sell);
        });
        this.charts.setOption({
          xAxis: {
            data: self.Xmath
          },
          series: [{ data: self.Y1math }, { data: self.Ymath }]
        });
        this.charts.resize();
        // console.log(this.Xmath)
        // console.log(this.Ymath)
        // console.log(this.Y1math)
      });
    },
    stop() {
      console.log("stop");
      var ws = new WebSocket("ws://192.168.1.27:8888/ws");
      var stop = { cmd: "stop" };
      ws.onopen = function(evt) {
        // console.log("Connection open ...");
        ws.send(JSON.stringify(stop));
        ws.close();
      };
    },
    start() {
      console.log("start");
      var ws = new WebSocket("ws://192.168.1.27:8888/ws");
      var start = { cmd: "start" };
      ws.onopen = function(evt) {
        // console.log("Connection open ...");
        ws.send(JSON.stringify(start));
        ws.close();
      };
    },
    init() {
      var ws = new WebSocket("ws://192.168.1.27:8888/ws");
      ws.onopen = function(evt) {
        console.log("Connection open ...");
        var aa = {
          req: "market.btcusdt.kline.1min",
          id: "id10"
        };
        ws.send(aa);
      };

      ws.onmessage = evt => {
        console.log("Received Message: " + JSON.stringify(evt.data));
        this.drawPie("hello", evt.data);
      };

      ws.onclose = evt => {
        console.log("Connection closed.");
        this.init();
      };
      ws.onerror = evt => {
        console.log("Connection error.");
        this.init();
      };
    }
  },
  created() {},
  //调用
  mounted() {
    // this.init();
    this.drawPie("hello");

    // this.$nextTick(function() {

    // })

    //  console.log(this.option.series[0].data)
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style >
h1,
h2 {
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
  color: #00ff8c;
}
.el-table th>.cell{
	text-align: center;
}
.el-table th, .el-table tr{
	border-bottom: 1px solid #eee;
}
table.hovertable {
	width: 100%;
	font-family: verdana,arial,sans-serif;
	font-size:11px;
	color:#333333;
	border-width: 1px;
	border-color: #999999;
	border-collapse: collapse;
}
table.hovertable th {
	background-color:#c3dde0;
	border-width: 1px;
	padding: 8px;
	border-style: solid;
	border-color: #a9c6c9;
}
table.hovertable tr {
	background-color:#d4e3e5;
}
table.hovertable td {
	border-width: 1px;
	padding: 8px;
	border-style: solid;
	border-color: #a9c6c9;
}

</style>
