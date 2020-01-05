<template>
    <div style="padding:1em">
        <h1>评论管理</h1>
        <el-button type="primary" size="small" @click="toAddHandler">添加</el-button>
        <el-button type="danger" size="small">批量删除</el-button>
    <el-table :data="comment.list">   
        <el-table-column fixed="left" prop="orderId" label="订单编号"></el-table-column>
        <el-table-column fixed="left" prop="content" label="内容"></el-table-column>
        <el-table-column fixed="left" prop="commentTime" label="评论时间"></el-table-column>
        <el-table-column prop="id" label="编号"></el-table-column>
        <el-table-column label="操作">
        <template v-slot="slot">
                    <el-button type="primary" size="small" icon="el-icon-delete" @click.prevent="toDeleteHandler(slot.row.id)"></el-button>
                    <el-button type="primary" size="small" icon="el-icon-edit" @click.prevent="toUpdateHandler(slot.row)"></el-button>
            </template>
            </el-table-column>
    </el-table>
<!--分页-->
 <el-pagination
    layout="prev, pager, next" :total="comment.total" @current-change="pageChageHandler">
  </el-pagination>

   <el-dialog
  :title="title"
  :visible.sync="visible"
  width="60%">
  <el-form :model="form" label-width="80px">
       <el-form-item label="内容">
          <el-input type="textarea" v-model="form.content"></el-input>
      </el-form-item>
  </el-form>
  <!-- :before-close="handleClose"> -->
  <span slot="footer" class="dialog-footer">
    <el-button size="small" @click="closeModalHandler">取 消</el-button>
    <el-button size="small" type="primary" @click="submitHandel">确 定</el-button>
  </span>
</el-dialog>
    </div>

</template>

<script>
import request from '@/utils/request'
import querystring from 'querystring'
export default {
     methods:{
          pageChageHandler(page){
            this.params.page=page-1;
            this.loadData();
        },
         submitHandel(){
             let url='http://localhost:6677/comment/saveOrUpdate'
            request({
                url,
                method:"POST",
                header:{
                    "Comtent-Type":"application/x-www-form-urlencoded"
                },
                data:querystring.stringify(this.form)
            }).then((response)=>{
                //模块框关闭
                this.closeModalHandler();
                //刷新
                this.loadData();
                //提示消息
                this.$message({
                    type:"success",
                    message:response.message
                });
            })
         },
         loadData(){
        let url = "http://localhost:6677/comment/query"
        request({
          url,
          method:"POST",
          headers:{
              "Content-Type":"application/x-www-form-urlencoded"
          },
          data:querystring.stringify(this.params)
        }).then((response)=>{
            this.comment=response.data;
        })
            //  //this->vue实例 即data和method中的数据
            //  let url="http://localhost:6677/comment/findAll"
            // request.get(url).then((response)=>{
            //     //箭头函数中的this指向的是外部函数的this
            //     this.comment=response.data;
            // })
         },
         toUpdateHandler(row){
             this.form=row;
             this.title="修改评论信息"
             this.visible=true;
         },
         toDeleteHandler(id){
             this.$confirm('此操作将永久删除该文件, 是否继续?', '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {
              let url="http://localhost:6677/comment/deleteById?id="+id;
              request.get(url).then((response)=>{
                this.loadData();
            this.$message({
            type: 'success',
            message:response.message
          });
          })
        })
        },
             closeModalHandler(){
            this.visible=false;
        },
        toAddHandler(){
            //将form变成初始值
        this.form={
                type:"content"
            }
             this.title="上传评论信息"
            this.visible=true;
        }},
    data(){
        return{
            title:"上传评论信息",
             visible:false,
            comment:{},
            form:{
                type:"comment"
            },
            params:{
                page:0,
                pageSize:7
            }
    }},
    created(){
        this.loadData()
        }
}

</script>

<style scoped>
    

</style>/