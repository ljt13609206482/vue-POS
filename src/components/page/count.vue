<template>
  <div class="count">
    <div class="count-title">
      <h2>数据统计</h2>
    </div>
    <div class="product-count-list">
      <el-tabs type="card">
        <el-tab-pane label=" 收入统计">
          <div id="myChart" :style="{width: '1000px', height: '500px'}"></div>
        </el-tab-pane>
        <el-tab-pane label="商品销量统计">
          <div class="charts-list">
            <div :style="{width: '300px', height: '220px'}">
              <p>汉堡销量对比(月)</p>
              <div id="hamburCount" :style="{width: '300px', height: '200px'}"></div>  
            </div>
            <div :style="{width: '300px', height: '220px'}">
              <p>小食销量对比(月)</p>
              <div id="snackCount" :style="{width: '300px', height: '200px'}"></div>  
            </div>
            <div :style="{width: '300px', height: '220px'}">
              <p>饮料销量对比(月)</p>
              <div id="drinkCount" :style="{width: '300px', height: '200px'}"></div>  
            </div>
            <div :style="{width: '300px', height: '220px'}">
              <p>套餐销量对比(月)</p>
              <div id="packageCount" :style="{width: '300px', height: '200px'}"></div>  
            </div>  
          </div>  
        </el-tab-pane>    
      </el-tabs>
    </div>
  </div>
</template>
<script>
import axios from "axios"
    export default{
      name: 'hello',
      data () {
          return {
            hamburId:'hamburCount',
            hamburData:[],
            snackId:'snackCount',
            snackData:[],
            drinkId:'drinkCount',
            drinkData:[
              {value:533,name:'可乐大杯'},
              {value:452,name:'雪顶咖啡'}
            ],
            packageId:'packageCount',
            packageData:[
              {value:241,name:'儿童欢乐套餐'},
              {value:576,name:'快乐全家桶'}
            ],
        }
      },
      mounted(){
        //向后台发送请求，获取所需数据
        axios.get('http://127.0.0.1:8081/xiangmu/vuePosData/product/countProduct.php')
        .then((response)=>{
          console.log(response);
            this.hamburData=response.data[0];
            this.drawPie(this.hamburId,this.hamburData);
            this.snackData=response.data[1];
            this.drawPie(this.snackId,this.snackData);
            this.drinkData=response.data[2]
            this.drawPie(this.drinkId,this.drinkData);
            this.packageData=response.data[3]
            this.drawPie(this.packageId,this.packageData);

        })
        .catch((err)=>{
          alert("网络错误，无法连接！")
        })
        //当逐渐挂载时，调用定义的函数
        this.drawLine();
        
      },
      methods: {
        drawLine(){
        // 基于准备好的dom，初始化echarts实例
        let myChart = this.$echarts.init(document.getElementById('myChart'))
        var days=[1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21];
        //创建数组，数据随机生成，实际数据为每日营业额
        var money=[];
        for(var i=0;i<days.length;i++){
          var num=parseInt(Math.random()*1000+2000);
          money.push(num)
        }
        // 绘制图表
        myChart.setOption({
            title: { text: '每日收入统计' },
            tooltip: {},
            xAxis: {
                name:'日期',
                data:days
            },
            yAxis: {},
            series: [{
                name: '收入',
                type: 'bar',
                data: money
            }]
          });
        },
        drawPie:function(countId,countData){
          let myChart = this.$echarts.init(document.getElementById(countId))
          myChart.setOption({
            tooltip : {
              trigger: 'item',
              formatter: "{a} <br/>{b} : {c} ({d}%)"
            },
            series : [
              {
                name:'商品销量',
                type: 'pie',
                radius: '40%',
                data:countData
              }
            ]
          })
        }
      }
    }
</script>
<style>
  .charts-list div{
    float: left;
    margin: 0px 80px;
  }
</style>