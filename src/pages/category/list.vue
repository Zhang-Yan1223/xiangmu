<template>
  <div>
      <h1>栏目管理</h1>
    <!-- 按钮 -->
    <el-button type="primary" size="small" @click="toAddHandler">添加</el-button> 
    <el-button type="danger" size="small" @click="closeModalHandler">批量删除</el-button>
    <!-- /按钮 -->
    <!-- 表格 -->
    <el-table :data="category">
      <el-table-column prop="id" label="编号"></el-table-column>
      <el-table-column prop="name" label="栏目名称"></el-table-column>
      <el-table-column prop="num" label="序号"></el-table-column>
      <el-table-column prop="parentId" label="父栏目"></el-table-column>
      <el-table-column prop="icon" label="图片"></el-table-column>
      <el-table-column label="操作">
        <template v-slot="slot">
          <a href="" @click.prevent="toDeleteHandler(slot.row.id)">删除</a>
          <a href="" @click.prevent="toUpdateHandler(slot.row)">修改</a>
        </template>
      </el-table-column>
    </el-table>
    <!-- /表格结束 -->
    <!-- 分页开始 -->
    <el-pagination layout="prev, pager, next" :total="50"></el-pagination>
    <!-- /分页结束 -->
    <!-- 模态框 -->
    <el-dialog
      :title="title"
      :visible.sync="visible"
      width="60%">
        <!-- ---{{form}} -->
      <el-form :model="form" label-width="80px">
        <el-form-item label="栏目名称">
          <el-input v-model="form.name"></el-input>
        </el-form-item>
        <el-form-item label="序号">
          <el-input v-model="form.num"></el-input>
        </el-form-item>
        <el-form-item label="父栏目">
          <el-input v-model="form.parentId"></el-input>
        </el-form-item>
        <el-form-item label="图片">
          <el-input v-model="form.icon"></el-input>
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
        loadData(){
            let url="http://localhost:6677/category/findAll"
        request.get(url).then((response)=>{
            //将查询结果设置到category
            this.category=response.data;
        })
        },
        submitHandler(){
            //通过request与
            let url = "http://localhost:6677/category/saveOrUpdate"
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
            this.title="录入栏目信息";
            //更新模态框
            this.form={
              type:"category"
            }
            this.visible=true;
        },
        closeModalHandler(){
            this.visible=false;
        },
        toUpdateHandler(row){
            this.title="修改栏目信息";
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
          let url = "http://localhost:6677/category/deleteById?id="+id;
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
            title:"录入栏目信息",
            visible:false,
            category:[],
            form:{
                type:"category"
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