<template>
    <div class="pos">
        <div>
            <el-row>
                <el-col :span="7" class="pos-order" id="order-list">
                    <el-tabs>
                        <el-tab-pane label="点餐" >
                            <el-table :data="tableData" border  style="width: 100%">
                                <el-table-column prop="goodsName" label="商品名称"></el-table-column> 
                                <el-table-column prop="count" label="数量" width="50"></el-table-column> 
                                <el-table-column prop="price" label="金额" width="70"></el-table-column> 
                                <el-table-column label="操作" width="100" fixed="right">
                                    <template slot-scope="scope">
                                        <el-button type="text" size="small" @click="delSingGoods(scope.row)">删除</el-button>
                                        <el-button type="text" size="small" @click="addDrderList(scope.row)">增加</el-button>
                                    </template>
                                </el-table-column>
                            </el-table>
                        </el-tab-pane>
                        <div class="number">
                            <small>数量：</small>{{totalCount}} <small style="margin-left:10px">总额：</small>{{totalMoney}}元 
                        </div>
                        <el-tab-pane label="挂单">
                            挂单
                        </el-tab-pane>
                        <el-tab-pane label="外卖">
                            外卖
                        </el-tab-pane>
                        <div style="padding:10px">
                            <el-button type="warning" >挂单</el-button>
                            <el-button type="danger" @click="delAllGoods(scope,row)">删除</el-button>
                            <el-button type="success" @click="checkout(scope,row)">结账</el-button>
                        </div>
                    </el-tabs>
                
                </el-col>
                <el-col :span="17">
                    <div class="often-goods">
                        <div class="title">常用商品</div>
                        <div class="often-goods-list">
                            <ul>
                                <li v-for="goods in oftenGoods" @click="addDrderList(goods)">
                                    <span>{{goods.goodsName}}</span>
                                    <span class="o-price">￥{{goods.price}}元</span>
                                </li>
                            </ul>
                        </div>
                    </div>

                    <div class="goods-type">
                        <el-tabs>
                            <el-tab-pane label="汉堡">
                                <div >
                                    <ul class="cookList">
                                        <li  v-for="goods in type0Goods" @click="addDrderList(goods)">
                                            <span class="foodImg">
                                                <img :src="goods.goodsImg" width="100%">
                                            </span>
                                            <span class="foodName">{{goods.goodsName}}</span>
                                            <span class="foodPrice">￥{{goods.price}}元</span>
                                        </li>
                                    </ul>
                                </div>    
                            </el-tab-pane>
                            <el-tab-pane label="小食">
                                <div >
                                    <ul class="cookList">
                                        <li  v-for="goods in type1Goods" @click="addDrderList(goods)">
                                            <span class="foodImg">
                                                <img :src="goods.goodsImg" width="100%">
                                            </span>
                                            <span class="foodName">{{goods.goodsName}}</span>
                                            <span class="foodPrice">￥{{goods.price}}元</span>
                                        </li>
                                    </ul>
                                </div>   
                            </el-tab-pane>
                            <el-tab-pane label="饮料">
                                <div >
                                    <ul class="cookList">
                                        <li  v-for="goods in type2Goods" @click="addDrderList(goods)">
                                            <span class="foodImg">
                                                <img :src="goods.goodsImg" width="100%">
                                            </span>
                                            <span class="foodName">{{goods.goodsName}}</span>
                                            <span class="foodPrice">￥{{goods.price}}元</span>
                                        </li>
                                    </ul>
                                </div>   
                            </el-tab-pane>
                            <el-tab-pane label="套餐">
                                <div >
                                    <ul class="cookList">
                                        <li  v-for="goods in type3Goods">
                                            <span class="foodImg">
                                                <img :src="goods.goodsImg" width="100%">
                                            </span>
                                            <span class="foodName">{{goods.goodsName}}</span>
                                            <span class="foodPrice">￥{{goods.price}}元</span>
                                        </li>
                                    </ul>
                                </div>   
                            </el-tab-pane>
                        </el-tabs>
                    </div>
                </el-col>
            </el-row>
        </div>
    </div>
</template>

<script>
import axios from 'axios'
export default {
  name: 'pos',
  data (){
      return {
        tableData: [],
        oftenGoods:[],
        type0Goods:[],
        type1Goods:[],
        type2Goods:[],
        type3Goods:[],
        totalCount: 0,
        totalMoney: 0
      }
  },
  //后端数据接口
  created: function (){
      axios.get('http://jspang.com/DemoApi/oftenGoods.php')
      .then(reponse=>{
        //   console.log(reponse);
          this.oftenGoods = reponse.data;
      })
      .catch(error=>{
          console.log(error)
          alert("网路错误")
      })

      axios.get('http://jspang.com/DemoApi/typeGoods.php')
      .then(reponse=>{
        //   console.log(reponse);
          this.type0Goods = reponse.data[0];
          this.type1Goods = reponse.data[1];
          this.type2Goods = reponse.data[2];
          this.type3Goods = reponse.data[3];
      })
      .catch(error=>{
          console.log(error)
          alert("网路错误")
      })
  },
  mounted: function (){
      var orderHeight = document.body.clientHeight;
      console.log(orderHeight);
      document.getElementById('order-list').style.height=orderHeight+'px';
  },
  methods: {
      addDrderList(goods){
        //   判断商品是否已经存在于订单列表中
        this.totalCount = 0;
        this.totalMoney = 0;
        let isHave = false;
        for(let i=0; i<this.tableData.length; i++){
            if(this.tableData[i].goodsId == goods.goodsId){
                isHave = true;
            }
        }

          //根据判断的值编写业务逻辑
        if(isHave){
            //改变列表中商品数量
            let arr = this.tableData.filter(o=>o.goodsId == goods.goodsId);
            arr[0].count++;
            // comsole.log(arr)
        }else{
            let newGoods = {goodsId: goods.goodsId, goodsName: goods.goodsName, price: goods.price, count: 1};
            this.tableData.push(newGoods)

        }
        this.getAllMoney();
        //计算总数量金额
        // this.tableData.forEach((element)=>{
        //     this.totalCount += element.count;
        //     this.totalMoney = this.totalMoney + (element.price * element.count);

        // })
      },
      //删除单个商品
      delSingGoods(goods){
          this.tableData = this.tableData.filter(o=>o.goodsId != goods.goodsId);
          this.getAllMoney();
        
      },
      //计算总数量和金额
      getAllMoney(){
          this.totalCount = 0;
          this.totalMoney = 0;
          if(this.tableData){
              this.tableData.forEach((element) =>{
                  this.totalCount += element.count;
                  this.totalMoney = this.totalMoney + (element.price * element.count);
              })
          }
      },
      delAllGoods(){
          this.tableData = [];
          this.totalCount = 0;
          this.totalMoney = 0;
      },
      checkout(){
          if(this.totalCount != 0){
              this.delAllGoods();
              this.$message({
                  message: '结账成功，感谢你又为店里出了一份力',
                  type: 'success'
              })
          }else{
              this.$message.error("购物车为空")
          }
      }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.el-tabs__item{
    padding: 0px 30px!important;
}
.pos-order{
    background: #f9fafc;
    border-right: 1px solid #c0ccda;
    height: 100%;
}
.title{
    height: 20px;
    border-bottom:1px solid #D3DCE6;
    background-color: #F9FAFC;
    padding:10px;
}
.often-goods-list ul li{
    list-style: none;
    float:left;
    border:1px solid #E5E9F2;
    padding:10px;
    margin:5px;
    background-color:#fff;
    cursor: pointer;
}
.o-price{
    color:#58B7FF; 
}
.goods-type{
    clear: both;
    cursor: pointer;
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
.number{
    padding: 10px;
    font-size: 18px;
    border-bottom: solid 1px #ebeef5;
}
</style>
