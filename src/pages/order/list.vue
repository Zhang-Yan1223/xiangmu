<template>
<!-- 订单管理 -->
    <div style="padding:1em">
        <h1>
            订单管理
        </h1>
      <!-- 按钮 -->
        <el-button type=“primary” size="small" @click="toAddHandler">添加</el-button>
        <el-button type="danger" size="small">批量删除</el-button>
        <!-- /按钮 -->
    <el-table :data="order">
        <el-table-column prop="id" label="订单编号"></el-table-column>
        <el-table-column prop="orderTime" label="下单时间"></el-table-column>
        <el-table-column prop="total" label="总价"></el-table-column>
        <el-table-column proper="status" label="状态"></el-table-column>
        <el-table-column proper="customerID" label="顾客id"></el-table-column>
        <el-table-column fixed="right" label="操作">
            <template v-slot="slot">
              <!-- {{slot.row}} -->
              <!-- 双大阔号显示脚本 -->
                <a href ="" @click.prevent="toDeleteHandler(slot.row.id)">删除</a>
                <a href="" @click.prevent="toUpdateHandler(slot.row)">修改</a>
            </template>
   </el-table-column>
    </el-table>
    <!-- /表格结束 -->
    <!--分页开始-->
    <el-pagination
    layout="prev, pager, next" :total="50">
  </el-pagination>
  <!-- /分页结束 -->
  <!--对话框-->
  <el-dialog
  :title="title"
  :visible.sync="visible"
  width="60%"
 >
 <el-form :model="form" label-width="80px">
   <el-form-item label="用户名">
     <el-input v-model="form.username"></el-input>
   </el-form-item>
   <el-form-item label="密码">
     <el-input type="password" v-model="form.password"></el-input>
     <!-- password定义，单词form拼写 -->
    </el-form-item>
         <el-form-item label="真实姓名">
           <el-input v-model="form.realname"></el-input>
         </el-form-item>
         <el-form-item label="手机号">
           <el-input v-model="form.telephone"> </el-input>
         </el-form-item>
         </el-form>
  <!-- <span>这是一段信息</span> -->
  <span slot="footer" class="dialog-footer">
    <el-button  @click="click = closeModalHand" size="small">取 消</el-button>
    <!--@表示事件绑定-->
    <el-button type="primary" @click="submitHandler" size="small">确 定</el-button>
  </span>
</el-dialog>
<!-- /模态框 -->
    </div>
</template>


<script>
import request from '@/utils/request'
import querystring from 'querystring'//把查询对象转换成字符串，方便传输
export default {
    //用于存放网页中需要调用的方法
    methods:{
      //重载员工数据
      loadDate(){
        let url = "http://localhost:6677/order/findAll"
        request.get(url).then((response)=>{
          //将查询结果设置到customers中，this指向外部函数的this
          this.order = response.data;
        })
      },
      submitHandler(){
      //this.form 对象 ---字符串--> 后台 {type:'customer',age:12}
      // json字符串 '{"type":"customer","age":12}'
      // request.post(url,this.form)
      // 查询字符串 type=customer&age=12
        //通过request与后台进行交互，并且要携带参数
        let url = "http://localhost:6677/order/saveOrUpdate"
        request({
          url,
          method:"POST",
          headers:{
              "Content-Type":"application/x-www-form-urlencoded"

          },
          data:querystring.stringify(this.form)
        }).then((response)=>{
          //模态框关闭
          this.closeModalHand();
          //提示消息
          this.$message({
            type:"success",
            message:response.message
          })
        })
      },
        toDeleteHandler(id){
            //确认
        this.$confirm('此操作将永久删除该文件, 是否继续?', '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {
            let url = "http://localhost:6677/order/deleteById?id="+id;
            request.get(url),then((response)=>{
              //刷新数据
              this.loadDate();
              //提示结果
              this.$message({
              type: 'success',
              message:response.message
            });
            })
             
        })
        },
        toAddHandler(){
          //将form变成初始值
          this.form={
            type:"order"
          }
            this.title="添加订单信息"
            this.visible=true;
        },
        closeModalHand(){
            this.visible=false;
        },
        toUpdateHandler(row){
                this.title="修改订单信息"
                  this.form=row;
                   this.visible = true;
      
        }
    },
    
   //暴露接口
    data(){
        //用于存放要向网页中存放的数据
        return{
            title:"添加订单信息",
            visible:false,
            order:[],
            form:{ type:"order"}
           
        }
    },
     created(){
         // this为当前vue实例对象
    // vue实例创建完毕 ，在页面加载出来的时候加载数据
    this.loadDate()

}
}
</script>

<style scoped>

</style>