<template>
  <div style="padding:1em">
      <h1>产品管理</h1>
    <el-button type="primary" size="small" @click="toAddHandlar">添加</el-button>
    <el-button type="danger" size="small">删除</el-button>
    <el-table :data="products">
        <el-table-column prop="id" label="编号"></el-table-column>
        <el-table-column prop="name" label="产品名称"></el-table-column>
        <el-table-column prop="price" label=" 价格"></el-table-column>
        <el-table-column prop="description" label="描述"></el-table-column>
        <el-table-column prop="photo" label="照片"></el-table-column>
        <el-table-column prop="categoryId" label="编号"></el-table-column>  
        <el-table-column prop="status" label="所属产品"></el-table-column>
        <el-table-column style="display:inline" label="操作">
            <el-row>
      <el-button-group style="margin-bottom:20px">
        <el-button size="small" type="primary" icon="el-icon-share" @click.prevent="toAddHandlar"></el-button>
        <el-button size="small" type="primary" icon="el-icon-edit" @click.prevent="toupdateHandler"></el-button>
        <el-button size="small" type="primary" icon="el-icon-delete" @click.prevent="toDeleteHandle"></el-button>
      </el-button-group>

    </el-row>
        </el-table-column>
    </el-table>
    <!-- 分页 -->
    <el-pagination
    background
    layout="prev, pager, next"
    :total="50">
    </el-pagination>
    <!-- 分页结束 -->
    <el-dialog
        title="录入产品信息"
        :visible.sync="visible"
        width="60%">
        {{form}}
        <!-- model双向数据绑定 -->
        <el-form :model="form" label-width="80px">
          <el-form-item label="产品名称" >
            <el-input v-model="form.name"></el-input>
          </el-form-item>
          <el-form-item label="编号" >
            <el-input v-model="form.id"></el-input>  
          </el-form-item>
          <el-form-item label="价格" >
            <el-input v-model="form.price"></el-input>  
          </el-form-item> 
          <el-form-item label="描述" >
            <el-input v-model="form.description"></el-input>  
          </el-form-item>
          <el-form-item label="所属产品" >
            <el-input v-model="form.status"></el-input>  
          </el-form-item>       
        </el-form>
        <!-- :before-close="handleClose" -->
        <!-- <span>这是一段信息</span> -->
        <span slot="footer" class="dialog-footer">
            <el-button @click="closeModelHandler">取消</el-button>
            <el-button type="primary" @click="submitHandle">确定</el-button>
        </span>
    </el-dialog>
  </div>
</template>

<script>
import request from '@/utils/request'
import querystring from 'querystring'
export default {
  methods: {
    loadDate(){
      let url = 'http://localhost:6677/product/findAll'
      request.get(url).then((response)=>{
        this.products = response.data;
      })
    },
    submitHandle() {
      //通过request与后台进行交互
      let url = 'http://localhost:6677/product/saveOrUpdate'
      // request.post(url.this.form);
      request({
        url,
        method : "post",
        headers:{
          "Content-Type":"application/x-www-form-urlencoded"
        },
        data : querystring.stringify(this.form)
      }).then((response)=>{
        //请求结束
        this.closeModelHandler();
        this.loadDate();
        this.$message({
          type:"success",
          $message:response.$message,
        })
      })
    },
    toDeleteHandle() {
      this.$confirm('确定删除这段数据？', {
        confirmButtonText: '是的',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(() => {
        this.$message({
          type: 'success',
          message: '已删除'
        })
      })
    },
    toupdateHandler() {
      this.visible = true
    },
    closeModelHandler() {
      this.visible = false
    },
    toAddHandlar() {
      this.visible = true
    }
  },
  data() {
    return {
      visible: false,
       form:{
         type : "product"
  },
      products : [{
      }]
    }
  },
  created() {
    // // vue实例创建完毕
    // let url = 'http://localhost:6677/customer/findAll'
    // request.get(url).then((response)=>{
    //   // 将查询结果设置到customers
    //   this.customers = response.data;
    // },
    this.loadDate();
  },
 
}
</script>
<style scoped>

</style>
