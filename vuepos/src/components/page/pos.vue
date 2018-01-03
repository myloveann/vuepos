<template>
  <div class="pos">
    <el-row>
      <el-col :span="7" class="pos-order" id="order-list">
        <el-tabs class="addpad">
          <el-tab-pane label="点餐">
            <el-table :data="tableData" border style="width:100%">
              <el-table-column prop="goodsName" label="商品名称"></el-table-column>
              <el-table-column prop="count" label="数量" width="50"></el-table-column>
              <el-table-column prop="price" label="金额" width="70"></el-table-column>
              <el-table-column prop="caozuo" label="操作" width="100"  fixed="right">
                <template slot-scope="scope">
                  <el-button type="text" size="samll" @click="addOrder(scope.row)">增加</el-button>
                  <el-button type="text" size="samll"  @click="delSingleGoods(scope.row)">删除</el-button>
                </template>
              </el-table-column>
            </el-table>

            <div>
              <small>数量:{{totalCount}}</small>
              <span style="display:inline-block;width:50px"></span>
              <small>金额:{{totalMoney}}</small>
            </div>

            <div class="div-btn">
              <el-button type="warning">挂单</el-button>
              <el-button type="danger" @click="delAllGoods()">删除</el-button>
              <el-button type="success" @click="checkOut()">结账</el-button>
            </div>
          </el-tab-pane>
          <el-tab-pane label="挂单">挂单</el-tab-pane>
          <el-tab-pane label="外卖">外卖</el-tab-pane>
        </el-tabs>

      </el-col>
      <el-col :span="17">
        <div class="often-goods">
          <div class="title">常用商品</div>
          <div class="often-goods-list">
            <ul>
                <li v-for="goods in oftenOrder" @click="addOrder(goods)">
                    <span>{{goods.goodsName}}</span>
                    <span class="o-price">￥{{goods.price}}元</span>
                </li>
            </ul>
          </div>
        </div>

       <div class="goods-type">
          <el-tabs >
            <el-tab-pane label="汉堡">
              <ul class='cookList'>
                  <li v-for="goods in type0Goods" @click="addOrder(goods)">
                      <span class="foodImg"><img :src="goods.goodsImg" width="100%"></span>
                      <span class="foodName">{{goods.goodsName}}</span>
                      <span class="foodPrice">￥{{goods.price}}元</span>
                  </li>
              </ul>
            </el-tab-pane>

            <el-tab-pane label="小吃">
              <ul class='cookList'>
                  <li v-for="goods in type1Goods" @click="addOrder(goods)">
                      <span class="foodImg"><img :src="goods.goodsImg" width="100%"></span>
                      <span class="foodName">{{goods.goodsName}}</span>
                      <span class="foodPrice">￥{{goods.price}}元</span>
                  </li>
              </ul>
            </el-tab-pane>

            <el-tab-pane label="饮料">
              <ul class='cookList'>
                  <li v-for="goods in type2Goods" @click="addOrder(goods)">
                      <span class="foodImg"><img :src="goods.goodsImg" width="100%"></span>
                      <span class="foodName">{{goods.goodsName}}</span>
                      <span class="foodPrice">￥{{goods.price}}元</span>
                  </li>
              </ul>
            </el-tab-pane>            

            <el-tab-pane label="套餐">
              <ul class='cookList'>
                  <li v-for="goods in type3Goods" @click="addOrder(goods)">
                      <span class="foodImg"><img :src="goods.goodsImg" width="100%"></span>
                      <span class="foodName">{{goods.goodsName}}</span>
                      <span class="foodPrice">￥{{goods.price}}元</span>
                  </li>
              </ul>
            </el-tab-pane> 
          </el-tabs>
       </div>

      </el-col>
    </el-row>
  </div>
</template>

<script>
import axios from "axios"; //引入对应的组件
export default {
  name: "pos",
  data() {
    return {
      tableData: [],
      oftenOrder: [],
      type0Goods: [],
      type1Goods: [],
      type2Goods: [],
      type3Goods: [],
      totalCount: 0,
      totalMoney: 0
    };
  },
  mounted: function() {
    var orderHeight = document.body.clientHeight;
    document.getElementById("order-list").style.height = orderHeight + "px";
  },
  created() {
    axios
      .get("http://jspang.com/DemoApi/oftenGoods.php")
      .then(response => {
        this.oftenOrder = response.data;
      })
      .catch(error => {
        alert("网络出错！！！");
      });

    axios
      .get("http://jspang.com/DemoApi/typeGoods.php")
      .then(response => {
        // console.log(response);
        this.type0Goods = response.data[0];
        this.type1Goods = response.data[1];
        this.type2Goods = response.data[2];
        this.type3Goods = response.data[3];
      })
      .catch(error => {
        console.log(error);
      });
  },
  methods: {
    addOrder(goods) {
      //每次点击都重置数量和钱
      this.totalCount = 0;
      this.totalMoney = 0;

      //判断点击的商品是否存在列表
      let isHave = false;
      for (let i = 0; i < this.tableData.length; i++) {
        if (this.tableData[i].goodsId == goods.goodsId) {
          isHave = true;
        }
      }

      //根据判断编写业务逻辑
      if (isHave) {
        //存在了数量加1
        let arr = this.tableData.filter(o => o.goodsId == goods.goodsId);
        arr[0].count++;
        this.getAllMoney();
      } else {
        //不存在则将该物品加入数组中
        //因为传入的goods和我们列表的goods不一样
        let newGoods = {
          goodsId: goods.goodsId,
          goodsName: goods.goodsName,
          price: goods.price,
          count: 1
        };
        this.tableData.push(newGoods);
        this.getAllMoney();
      }
    },
    //删除单个商品
    delSingleGoods(goods) {
      this.tableData = this.tableData.filter(o => o.goodsId != goods.goodsId);
      this.getAllMoney();
    },
    //删除所有商品
    delAllGoods() {
      this.tableData = [];
      this.totalCount = 0;
      this.totalMoney = 0;
    },
    //汇总数量金额
    getAllMoney() {
      this.totalCount = 0;
      this.totalMoney = 0;
      if (this.tableData) {
        //计算价格
        this.tableData.forEach(element => {
          this.totalCount += element.count;
          this.totalMoney = this.totalMoney + element.price * element.count;
        });
      }
    },

    //模拟结账
    checkOut(){
      if(this.totalCount!=0){
        this.tableData=[];
        this.totalCount=0;
        this.totalMoney=0;
        this.$message({
          message:'结账成功，感谢你又为店里出来一份力！',
          type:'success'
        })
      }else{
        this.$message.error("不能空结账!");
      }
    }
  }
};
</script>


<style scoped>
.pos-order {
  background-color: #f9fafc;
  border-right: 1px solid #c0ccda;
}
.div-btn {
  margin-top: 10px;
}
.title {
  height: 20px;
  border-bottom: 1px solid #d3dce6;
  background-color: #f9fafc;
  padding: 10px;
}
.often-goods-list ul li {
  list-style: none;
  float: left;
  border: 1px solid #e5e9f2;
  padding: 10px;
  margin: 5px;
  background-color: #fff;
}
.o-price {
  color: #58b7ff;
}
.goods-type {
  clear: both;
  margin-left: 10px;
}
.cookList li {
  list-style: none;
  width: 23%;
  border: 1px solid #e5e9f2;
  height: auot;
  overflow: hidden;
  background-color: #fff;
  padding: 2px;
  float: left;
  margin: 2px;
}
.cookList li span {
  display: block;
  float: left;
}
.foodImg {
  width: 40%;
}
.foodName {
  font-size: 16px;
  padding-left: 10px;
  color: brown;
}
.foodPrice {
  font-size: 16px;
  padding-left: 10px;
  padding-top: 10px;
}
.addpad {
  padding-left: 0.05rem;
}

.often-goods-list ul li:hover,
.cookList li:hover {
  cursor: pointer;
}
</style>
