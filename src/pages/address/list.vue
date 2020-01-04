<template>
  <div style="padding:1em">
      <h1>地址管理</h1>
    <!-- 按钮 -->
    <el-button type="primary" size="small" @click="toAddHandler">添加</el-button> 
    <el-button type="danger" size="small" @click="closeModalHandler">批量删除</el-button>
    <!-- /按钮 -->
    <!-- 表格 -->
    <el-table :data="address.list">
      <el-table-column prop="id" label="编号"></el-table-column>
      <el-table-column prop="province" label="所在省"></el-table-column>
      <el-table-column prop="city" label="所在市"></el-table-column>
      <el-table-column prop="area" label="所在区"></el-table-column>
      <el-table-column prop="address" label="地址"></el-table-column>
      <el-table-column prop="telephone" label="电话"></el-table-column>
      <el-table-column prop="customerId" label="顾客序号"></el-table-column>
      <el-table-column label="操作">
        <template v-slot="slot">
          <el-button type="primary" size="small" icon="el-icon-delete" @click.prevent="toDeleteHandler(slot.row.id)"></el-button>
          <el-button type="primary" size="small" icon="el-icon-edit" @click.prevent="toUpdateHandler(slot.row)"></el-button>
        </template>
      </el-table-column>
    </el-table>
    <!-- /表格结束 -->
    <!-- 分页开始 -->
    <el-pagination 
    layout="prev, pager, next" 
    :total="address.total"
    @current-change="pageChangeHandler"
    ></el-pagination>
    <!-- /分页结束 -->
    <!-- 模态框 -->
    <el-dialog
      :title="title"
      :visible.sync="visible"
      width="60%">
        <!-- ---{{form}} -->
      <el-form :model="form" label-width="80px">
        <el-form-item label="所在省">
          <el-input v-model="form.province"></el-input>
        </el-form-item>
        <el-form-item label="所在市">
          <el-input v-model="form.city"></el-input>
        </el-form-item>
        <el-form-item label="所在区">
          <el-input v-model="form.area"></el-input>
        </el-form-item>
        <el-form-item label="地址">
          <el-input v-model="form.address"></el-input>
        </el-form-item>
        <el-form-item label="电话">
          <el-input v-model="form.telephone"></el-input>
        </el-form-item>
        
      </el-form>
    <span slot="footer" class="dialog-footer">
    <el-button size="small" @click="closeModalHandler">取 消</el-button>
    <el-button size="small" type="primary" @click="submitHandler">确 定</el-button>
  </span>
</el-dialog>

    </div>   
</template>

<script>
import request from '@/utils/request'
import querystring from 'querystring'
export default {
    //存放调用方法
    methods:{
        pageChangeHandler(page){
        // 将params中当前页改为插件中的当前页
        this.params.page = page-1;
        // 加载
        this.loadData();
    },
    loadAddress(){
      let url = "http://localhost:6677/address/findAll"
      request.get(url).then((response)=>{
        // 将查询结果设置到products中，this指向外部函数的this
        this.options = response.data;
      })
    },
        loadData(){
            let url="http://localhost:6677/address/query"
        request({
          url,
          method:"post",
          headers:{
              "Content-Type":"application/x-www-form-urlencoded"
          },
          data:querystring.stringify(this.params)
      }).then((response)=>{
        // 将查询结果设置到products中，this指向外部函数的this
        this.address = response.data;
      })
        },
        submitHandler(){
            //通过request与
            let url = "http://localhost:6677/address/saveOrUpdate"
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
                //刷新
                this.loadData();
                this.$message({
                    type:"success",
                    message:response.message
                })
            })
        },
        toAddHandler(){
            this.title="录入地址信息";
            //更新模态框
            this.form={
              type:"address"
            }
            this.visible=true;
        },
        closeModalHandler(){
            this.visible=false;
        },
        toUpdateHandler(row){
            this.title="修改地址信息";
            this.form=row;
            this.visible=true;
        },
        toDeleteHandler(id){
            //确认
            this.$confirm('此操作将永久删除该文件, 是否继续?', '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {
          //     调用后台接口         查看 alert(id);
          let url = "http://localhost:6677/address/deleteById?id="+id;
          request.get(url).then((response)=>{
            //刷新数据
            this.loadData();
            //提示结果
            this.$message({
            type: 'success',
            message: response.message
          });
          })
        })
        }
    },
    //存放要向网页中存放的数据
    data(){
        
        return{
            title:"录入地址信息",
            visible:false,
            address:{},
            form:{
                type:"address"
            },
            params:{
          page:0,
          pageSize:10
      }
        }
    },
    created(){
        //vue实例
        this.loadData();
    }
}
</script>

<style scoped>

</style>