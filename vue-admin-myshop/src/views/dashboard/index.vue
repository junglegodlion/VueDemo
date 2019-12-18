<template>
  <div class="dashboard-container">
    <div class="title">
      <h1>石化场地环境监测预警-风险管控智能管理平台</h1>
      <img src="./大数据分析.jpg" />
    </div>
  </div>
</template>

<script>
import { mapGetters } from 'vuex'

export default {
  name: 'Dashboard',
  computed: {
    ...mapGetters([
      'name'
    ])
  }
}
</script>

<style lang="scss" scoped>
.title{
  text-align: center;
}
</style>
