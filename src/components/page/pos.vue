<template>
  <div class="pos">
    <el-row>
      <el-col :span="7" class="pos-order" id="order-list">
        <el-tabs>
          <el-tab-pane label="点餐">
            <el-table :data="tableData" border style="width:100%" >
              <el-table-column prop="goodsName" label="商品名称"></el-table-column>
              <el-table-column prop="count" label="数量"></el-table-column>
              <el-table-column prop="price" label="金额"></el-table-column>
              <el-table-column label="商品名称" fixed="right">
                <template slot-scope="scope">
                  <el-button type="text" size="small" @click="delSingleGoods(scope.row)">删除</el-button>
                  <el-button type="text" size="small" @click="addOrderList(scope.row)">增加</el-button>
                </template>
              </el-table-column>
            </el-table>
          </el-tab-pane>
          <el-tab-pane label="挂单">
            挂单
          </el-tab-pane>
          <el-tab-pane label="外卖">
            外卖
          </el-tab-pane>
        </el-tabs>
        <div class="total-div">
            <p>数量:{{totalCount}}</p>
            <p>金额:{{totalMoney}}</p>
        </div>
        <div class="div-tab">
          <el-button type="warning">挂单</el-button>
          <el-button type="danger" @click="delAllGoods()">删除</el-button>
          <el-button type="success" @click="checkout()">结账</el-button>
        </div>
      </el-col>
      <el-col :span="17">
        <div class="often-goods">
          <div class="title">常用商品</div>
          <div class="o-goods-list">
            <ul>
              <li v-for="(goods,index) in oftenGoods" :key="index" @click="addOrderList(goods)">
                <span>{{goods.goodsName}}</span>
                <span class="o-price">{{goods.price}}</span>
              </li>
            </ul>
          </div>
        </div>
        <div class="goods-type">

          <el-tabs>
            <el-tab-pane label="汉堡">
              <ul class='cookList'>
                <li v-for="goods in type0Goods" @click="addOrderList(goods)">
                  <span class="foodImg"><img :src="goods.goodsImg" width="100%"></span>
                  <span class="foodName">{{goods.goodsName}}</span>
                  <span class="foodPrice">￥{{goods.price}}元</span>
                </li>
              </ul>
            </el-tab-pane>
            <el-tab-pane label="小食">
              <ul class='cookList'>
                <li v-for="goods in type1Goods" @click="addOrderList(goods)">
                  <span class="foodImg"><img :src="goods.goodsImg" width="100%"></span>
                  <span class="foodName">{{goods.goodsName}}</span>
                  <span class="foodPrice">￥{{goods.price}}元</span>
                </li>
              </ul>
            </el-tab-pane>
            <el-tab-pane label="饮料">
              <ul class='cookList'>
                <li v-for="goods in type2Goods" @click="addOrderList(goods)">
                  <span class="foodImg"><img :src="goods.goodsImg" width="100%"></span>
                  <span class="foodName">{{goods.goodsName}}</span>
                  <span class="foodPrice">￥{{goods.price}}元</span>
                </li>
              </ul>
            </el-tab-pane>
            <el-tab-pane label="套餐">
              <ul class='cookList'>
                <li v-for="goods in type3Goods">
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
import axios from 'axios'
export default {
  name: 'pos',
  data(){
    return {
      tableData: [],
      oftenGoods:[],
      type0Goods:[],
      type1Goods:[],
      type2Goods:[],
      type3Goods:[],
      totalMoney:0,
      totalCount:0
    }
  },
  created:function(){
    axios.get('http://jspang.com/DemoApi/oftenGoods.php')
      .then(res =>{
        this.oftenGoods = res.data
      })
      .catch(err =>{
        alert('网络错了')
      })

    axios.get('http://jspang.com/DemoApi/typeGoods.php')
        .then(response=>{
          console.log(response);
          //this.oftenGoods=response.data;
          this.type0Goods=response.data[0];
          this.type1Goods=response.data[1];
          this.type2Goods=response.data[2];
          this.type3Goods=response.data[3];

        })
        .catch(error=>{
          console.log(error);
          alert('网络错误，不能访问');
        })
  },
  mounted:function(){
    let orderHieght = document.body.clientHeight;
    document.getElementById('order-list').style.height = orderHieght + 'px'
  },
  methods:{
    addOrderList(goods){
      this.totalMoney = 0;
      this.totalCount = 0;
      let isHave = false;
      for(let i=0;i<this.tableData.length;i++){
        if(this.tableData[i].goodsId==goods.goodsId){
          isHave = true
        }
      }
      if(isHave){
        //存在就进行数量添加
        let arr = this.tableData.filter(o =>o.goodsId == goods.goodsId);
        arr[0].count++;
        //console.log(arr);
      }else{
        //不存在就推入数组
        let newGoods={goodsId:goods.goodsId,goodsName:goods.goodsName,price:goods.price,count:1};
        this.tableData.push(newGoods);
      }
      this.tableData.forEach((el)=>{
        this.totalCount+=el.count;
        this.totalMoney=this.totalMoney+(el.price*el.count);
      })
      this.getAllMoney()
    },
    //删除单个
    delSingleGoods(goods){
      this.tableData=this.tableData.filter(o => o.goodsId !=goods.goodsId);
      this.getAllMoney()
    },
    //模拟结账
    checkout() {
      if (this.totalCount!=0) {
        this.tableData = [];
        this.totalCount = 0;
        this.totalMoney = 0;
        this.$message({
          message: '结账成功，感谢你又为店里出了一份力!',
          type: 'success'
        });

      }else{
        this.$message.error('不能空结。老板了解你急切的心情！');
      }

    },
    //删除所有
    delAllGoods() {
      this.tableData = [];
      this.totalCount = 0;
      this.totalMoney = 0;
    },
    getAllMoney(){
      this.totalCount=0;
      this.totalMoney=0;
      if(this.tableData){
        this.tableData.forEach((element) => {
          this.totalCount+=element.count;
          this.totalMoney=this.totalMoney+(element.price*element.count);
        });
      }

    }

  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
  .pos-order{
    background-color:#f9fafc;
    border-right:1px solid #c0ccda;
    height:100%;
  }
  .pos{
    text-align: center;
  }
  .div-tab{
    margin-top:20px;
  }
  .title{
    height:18px;
    padding:10px;
    border-bottom:1px solid #ddd;
    background-color: #f9fafc;
    text-align: left;
  }
  .o-goods-list ul li{
    list-style: none;
    float:left;
    border-radius: 5px;
    border:1px solid #1089a9;
    background-color: #f9fafc;
    padding:10px;
    margin:10px;
  }
  .o-price{
    color:red;
  }
  .goods-type{
    clear:both
  }
  .cookList li{
    list-style: none;
    width:23%;
    border:1px solid #E5E9F2;
    height: auot;
    overflow: hidden;
    background-color:#fff;
    padding: 2px;
    float:left;
    margin: 2px;

  }
  .cookList li span{

    display: block;
    float:left;
  }
  .foodImg{
    width: 40%;
  }
  .foodName{
    font-size: 18px;
    padding-left: 10px;
    color:brown;

  }
  .foodPrice{
    font-size: 16px;
    padding-left: 10px;
    padding-top:10px;
  }
  .total-div p{
    margin:0;
    padding:10px;
    border-bottom:1px solid #ddd;
    background:#fff;
  }
</style>
