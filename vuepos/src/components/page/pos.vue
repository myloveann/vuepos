<template>
  <div class="pos">
    <el-row>
      <el-col :span="7" class="pos-order" id="order-list">我是订单栏</el-col>
      <el-col :span="17">我是产品栏</el-col>
    </el-row>
  </div>
</template>

<script>
export default {
  name: 'pos',
  data () {
    return {
      msg: 'Hello AwesomePos demo'
    }
  },
  mounted:function(){
    var orderHeight=document.body.clientHeight;
    document.getElementById('order-list').style.height=orderHeight+'px'
  }
}
</script>


<style scoped>
.pos-order{
  background-color: #F9FAFC;
  border-right: 1px solid #C0CCDA;

}
</style>
