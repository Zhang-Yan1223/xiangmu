<template>
<!-- 顾客管理 -->
    <div style="padding:1em">
        <h1>
            顾客管理
        </h1>
      <!-- 按钮 -->
        <el-button type="primary" size="small" @click="toAddHandler">添加</el-button>
        <el-button type="danger" size="small"  @click="closeModalHandler">批量删除</el-button>
        <!-- /按钮 -->
    <el-table :data="customer.list">
        <el-table-column prop="id" label="编号"></el-table-column>
        <el-table-column prop="username" label="用户名"></el-table-column>
        <el-table-column prop="realname" label="真实姓名"></el-table-column>
        <el-table-column prop="telephone" label="联系方式"></el-table-column>
        <el-table-column fixed="right" label="操作">
            <template v-slot="slot">
              <!-- {{slot.row}} -->
              <!-- 双大阔号显示脚本 -->
                <el-button type="primary" size="small" icon="el-icon-delete" @click.prevent="toDeleteHandler(slot.row.id)"></el-button>
                <el-button type="primary" size="small" icon="el-icon-edit" @click.prevent="toUpdateHandler(slot.row)"></el-button>
            </template>
   </el-table-column>
    </el-table>
    <!-- /表格结束 -->
    <!--分页开始-->
    <el-pagination 
    layout="prev, pager, next" 
    :total="customer.total"
    @current-change="pageChangeHandler"
    ></el-pagination>
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
    <el-button size="small" @click="closeModalHandler">取 消</el-button>
    <!--@表示事件绑定-->
    <el-button type="primary" size="small" @click="submitHandler">确 定</el-button>
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
       pageChangeHandler(page){
        // 将params中当前页改为插件中的当前页
        this.params.page = page-1;
        // 加载
        this.loadData();
    },
    loadCustomer(){
      let url = "http://localhost:6677/customer/findAll"
      request.get(url).then((response)=>{
        // 将查询结果设置到products中，this指向外部函数的this
        this.options = response.data;
      })
    },
      //重载员工数据
      loadData(){
            let url="http://localhost:6677/customer/query"
        request({
          url,
          method:"post",
          headers:{
              "Content-Type":"application/x-www-form-urlencoded"
          },
          data:querystring.stringify(this.params)
      }).then((response)=>{
        // 将查询结果设置到products中，this指向外部函数的this
        this.customer = response.data;
      })
      },
      submitHandler(){
      //this.form 对象 ---字符串--> 后台 {type:'customer',age:12}
      // json字符串 '{"type":"customer","age":12}'
      // request.post(url,this.form)
      // 查询字符串 type=customer&age=12
        //通过request与后台进行交互，并且要携带参数
        let url = "http://localhost:6677/customer/saveOrUpdate"
        request({
          url,
          method:"POST",
          headers:{
              "Content-Type":"application/x-www-form-urlencoded"

          },
          data:querystring.stringify(this.form)
        }).then((response)=>{
          //模态框关闭
          this.closeModalHandler();
          this.loadData();
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
            let url = "http://localhost:6677/customer/deleteById?id="+id;
            request.get(url),then((response)=>{
              //刷新数据
              this.loadData();
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
            type:"customer"
          }
            this.title="添加顾客信息"
            this.visible=true;
        },
        closeModalHandler(){
            this.visible=false;
        },
        toUpdateHandler(row){
                this.title="修改顾客信息"
                  this.form=row;
                   this.visible = true;
      
        }
    },
    
   //暴露接口
    data(){
        //用于存放要向网页中存放的数据
        return{
            title:"添加顾客信息",
            visible:false,
            customer:{},
            form:{ type:"customer"},
            params:{
          page:0,
          pageSize:10
      }
        }
    },
     created(){
         // this为当前vue实例对象
    // vue实例创建完毕 ，在页面加载出来的时候加载数据
    this.loadData()

}
}
</script>

<style scoped>

</style>