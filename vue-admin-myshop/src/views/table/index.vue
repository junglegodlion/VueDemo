<template>
  <div class="hello" style=" text-align: center; ">
    <div style="display: inline-block;">
      <!--        <span class="location"> 地点: </span>-->
      <select id="siteid" οnchange="selectcity()" @change="getSite($event)">
        <option >---请选择地区---</option>
        <option v-for="item in selected" :value='item'> {{item}}</option>
      </select>
    </div>
    <div style="display: inline-block">
      <!--        <span class="location"> 地点: </span>-->
      <select  id="startid" οnchange="selectcity()" @change="getStart($event)">
        <option >---请选择起始日期---</option>
        <option v-for="time in times" :value='time'> {{time}}</option>
      </select>
    </div>
    <div style="display: inline-block">
      <!--        <span class="location"> 地点: </span>-->
      <select  id="stopid" οnchange="selectcity()" @change="getStop($event)">
        <option >---请选择终止日期---</option>
        <option v-for="time in times" :value='time'> {{time}}</option>
      </select>
    </div>
    <div style="display: inline-block">
      <select  id="pollutionid" οnchange="selectcity()" @change="getPollution($event)">
        <option  >---请选择物质---</option>
        <option>铅</option>
        <option>石油</option>
        <option>汞</option>
      </select>
    </div>
    <!-- 按钮 -->
    <div style="display: inline-block">
      <button type="button" id="btn_submit" v-on:click="sayHi()"> 查询 </button>
    </div>
    <chart class="zheixan" ref="charts" :options="orgOptions" :auto-resize="true"></chart>
  </div>
</template>

<script>
  /* eslint-disable */
  import axios from 'axios'
  export default {
    name: 'Charts',
    data () {
      return {
        orgOptions: {
          xAxis: {
            type: 'category',
            data: []
          },
          yAxis: {
            type: 'value'
          },
          series: [{
            data: [],
            type: 'line',
            smooth: true
          }]
        },
        selected: [],
        times: [],
        query: {
          site: "",
          start: "",
          stop: "",
          pollution: ""
        },
        temp: []
      }
    },
    methods: {
      fetchData(){
        axios
          .get('http://192.168.1.18:9091/map/site')
          .then(response => (this.selected=response.data));

      },
      fetchTimes(){
        axios
          .get('http://192.168.1.18:9091/map/location')
          .then(response => (this.times=response.data));

      },
      getSite: function (event1) {
        this.query.site =  event1.target.value;
        // this.query.site =  event.target.value;
        console.log(this.query.site)
      },
      getStart: function (event2) {
        this.query.start =  event2.target.value;
        // this.query.site =  event.target.value;
        console.log(this.query.start)
      },
      getStop: function (event3) {
        this.query.stop =  event3.target.value;
        // this.query.site =  event.target.value;
        console.log(this.query.stop)
      },
      getPollution: function (event4) {
        this.query.pollution =  event4.target.value;
        // this.query.site =  event.target.value;
        console.log(this.query.pollution)
      },
      sayHi: function (event) {
        var list1=new Array();
        var list2=new Array();
        axios
          .get('http://192.168.1.18:9091/map/line',{
            params: {
              siteid: this.query.site,
              startid: this.query.start,
              stopid: this.query.stop,
              pollutionid: this.query.pollution
            }
          }).then(response => {
          for (var i = 0;i < response.data.length; ++i){
            list1[i] = response.data[i]["times"];
            list2[i] = response.data[i]["count"];
            this.orgOptions.xAxis.data = list1;
            this.orgOptions.series[0].data = list2
          }
        });
        // console.log(this.temp);





      }

    },
    created() {
      this.fetchData();
      this.fetchTimes();
    }

  }
</script>
<style scoped>

  /*设置标题样式*/
  .login-title {
    text-align: center;
  }

  /*设置登录框样式*/
  .zheixan {

    margin: 0 auto;
    /*登录框整体下移*/
    margin-top: 150px;
    /*阴影效果,有立体的效果*/
    box-shadow: 0 0 20px;
  }
</style>
