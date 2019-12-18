<template>
  <div class="box">
    <div style="text-align: center; margin-top: 20px;">
      <div style="display: inline-block">
        <!--        <span class="location"> 地点: </span>-->
        <select  id="sid"  @change="getDate($event)">
          <option>---请选择日期---</option>
          <option v-for="time in times" :value='time'> {{time}}</option>
        </select>
      </div>
      <div style="display: inline-block">
        <select  id="pollutionid"  @change="getPollution($event)">
          <option>---请选择物质---</option>
          <option>铅</option>
          <option>石油</option>
          <option>汞</option>
        </select>
      </div>
      <div style="display: inline-block">
        <button type="button" id="btn_submit" v-on:click="query()"> 查询 </button>
      </div>

    </div>
    <div id="container" class="reli"></div>
  </div>
</template>

<script>
  /* eslint-disable */
  import AMap from 'AMap' // 引入高德地图
  import axios from 'axios'
  export default {
    data(){
      return {
        times: [],
        date: "",
        pollution: "",
        res_data: null,
        res_map: null,
        heatmap2: null
      }
    },
    created() {
      this.findDate();
    },
    mounted () {

      this.init();
    },
    methods: {
      init () {
        var map = new AMap.Map("container", {
          resizeEnable: true,
          center: [106.55,29.57],
          zoom: 7
        });

        map.setFeatures(['road','point'])//多个种类要素显示
        //测量距离
        map.plugin(["AMap.MouseTool"],function(){
          var mousetool = new AMap.MouseTool(map);
          mousetool.rule(); //使用鼠标工具，在地图上画标记点
          mousetool.measureArea(); //使用鼠标工具，测量面积
        });


        //-------------------------------------------
        // var heatmap2_here = this.heatmap2;  //test

        // var test = this.res_data;
        var _this = this;
        map.plugin(["AMap.Heatmap"], function () {
          //初始化heatmap对象

          _this.heatmap2 = new AMap.Heatmap(map, {   //test

            radius: 25, //给定半径
            opacity: [0, 0.8]
            ,
            gradient:{
              0.5: 'blue',
              0.65: 'rgb(117,211,248)',
              0.7: 'rgb(0, 255, 0)',
              0.9: '#ffea00',
              1.0: 'red'
            }
          });
          console.log("实例化后， _this.heatmap2 = " + _this.heatmap2);
          // 设置数据集：该数据为北京部分“公园”数据
          _this.heatmap2.setDataSet({
            //lng为经度值，lat为纬度值，count为权重
            data:test,
            //权重最大值
            max: 1
          });

          //工具条
          AMap.plugin(['AMap.ToolBar'],function(){//异步同时加载多个插件
            var toolbar = new AMap.ToolBar();
            map.addControl(toolbar);

          });
        });

        map.setFeatures(['road','point'])//多个种类要素显示
        //测量距离
        map.plugin(["AMap.MouseTool"],function(){
          var mousetool = new AMap.MouseTool(map);
          mousetool.rule(); //使用鼠标工具，在地图上画标记点
          mousetool.measureArea(); //使用鼠标工具，测量面积
        });

        if (!isSupportCanvas()) {
          alert('热力图仅对支持canvas的浏览器适用,您所使用的浏览器不能使用热力图功能,请换个浏览器试试~')
        }

        //判断浏览区是否支持canvas
        function isSupportCanvas() {
          var elem = document.createElement('canvas');
          return !!(elem.getContext && elem.getContext('2d'));
        }
      },
      findDate(){
        axios
          .get('http://192.168.1.18:9091/map/location')
          .then(response => (this.times=response.data));
      },
      getDate(event){
        this.date =  event.target.value;
        // this.query.site =  event.target.value;
        console.log(this.date)
        this.query();
      },
      getPollution(event){
        this.pollution =  event.target.value;
        // this.query.site =  event.target.value;
        console.log(this.pollution)
        this.query();
      },
      query() {
        var _this = this;

        axios
          .get('http://192.168.1.18:9091/map/data',{
            params: {
              sid: this.date,
              pollutionid: this.pollution
            }
          }).then(response => {

          //------------------------------------
          // 设置数据集
          _this.heatmap2.setDataSet({
            //lng为经度值，lat为纬度值，count为权重
            data: response.data,
            //权重最大值
            max: 1
          });
        });
      }
    }
  }
</script>
<style scoped>


  .reli {

    margin: 0 auto;
    /*登录框整体下移*/
    margin-top: 0px;
    /*阴影效果,有立体的效果*/
    /*box-shadow: 0 0 10px;*/
    width: 1500px;
    height: 800px;
  }
</style>
