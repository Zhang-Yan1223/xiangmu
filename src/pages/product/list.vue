<template>
    <div>
      <!-- 按钮 -->
        <el-button type=“success” size="small" @click="toAddHandler">添加</el-button>
        <el-button type="danger" size="small">批量删除</el-button>
        <!-- /按钮 -->
    <el-table :data="product">
        <el-table-column prop="id" label="编号"></el-table-column>
        <el-table-column prop="name" label="产品名称"></el-table-column>
        <el-table-column prop="price" label="价格"></el-table-column>
        <el-table-column proper="description" label="描述"></el-table-column>
        <el-table-column proper="categoryId" label="所属产品"></el-table-column>
        <el-table-column fixed="right" label="操作">
            <template v-slot="slot">
              <!-- {{slot.row}} -->
              <!-- 双大阔号显示脚本 -->
                <a href ="" @click.prevent="toDeleteHandler(slot.row.id)">删除</a>
                <a href="" @click.prevent="toUpdateHandler(slot.row.ip)">修改</a>
            </template>
   </el-table-column>
    </el-table>
    <!-- /表格结束 -->
    <!--分页开始-->
    <!-- <el-pagination
    layout="prev, pager, next" :total="50">
  </el-pagination> -->
  <!-- /分页结束 -->
  <!--对话框-->
  <el-dialog
  title="录入产品信息"
  :visible.sync="visible"
  width="60%"
 >
 --{{form}}
 <el-form :model="form" label-width="80px">
   <el-form-item label="名称">
     <el-input v-model="form.name"></el-input>
   </el-form-item>
   <el-form-item label="价格">
     <el-input type="price" v-model="form.price"></el-input>
     <!-- password定义，单词form拼写 -->
     <el-form-item label="所属栏目">
       <el-select v-model="form.categoryID">
         <el-option
           v-for="item in options"
           :key="item.id"
           :label="item.name"
           :value="item.id">
         </el-option>
       </el-select>

     </el-form-item>
    </el-form-item>
         <el-form-item label="介绍">
           <el-input v-model="form.description"></el-input>
         </el-form-item>
  <!-- <span>这是一段信息</span> -->
  </el-form>
  <span slot="footer" class="dialog-footer">
    <el-button  @click="click = closeModalHander" size="small">取 消</el-button>
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
      loadCategory(){
        let url = "http://localhost:6677/category/findAll"
        request.get(url).then((response)=>{
           // 将查询结果设置到products中，this指向外部函数的this
        this.options = response.data;
        })
      },
      //重载员工数据
      loadDate(){
        let url = "http://localhost:6677/product/findAll"
        request.get(url).then((response)=>{
          //将查询结果设置到customers中，this指向外部函数的this
          this.product = response.data;
        })
      },
      submitHandler(){
      //this.form 对象 ---字符串--> 后台 {type:'customer',age:12}
      // json字符串 '{"type":"customer","age":12}'
      // request.post(url,this.form)
      // 查询字符串 type=customer&age=12
        //通过request与后台进行交互，并且要携带参数
        let url = "http://localhost:6677/product/saveOrUpdate"
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
          let url = "http://localhost:6677/product/deleteById?id="+id;
          request.get(url).then((response)=>{
            this.loadDate(),
            this.$message({
              type:'success',
              message:response.message
            })
          })

          });
        },
        toAddHandler(){
            this.form={}
            this.visible=true;
        },
        closeModalHand(){
            this.visible=false;
        },
        toUpdateHandler(row){
      // 模态框表单中显示出当前行的信息
      this.form = row;
      this.visible = true;
    },

    closeModalHandler(){
      this.visible = false;
    },
    toAddHandler(){
      // 将form变为初始值
      this.form = {}
      this.visible = true;

  },

  // 用于存放要向网页中显示的数据
    },
    
   //暴露接口
    data(){
        //用于存放要向网页中存放的数据
        return{
            visible:false,
            product:[],
            options:[],
            form:{}
           
        }
    },
     created(){
         // this为当前vue实例对象
    // vue实例创建完毕 ，在页面加载出来的时候加载数据
    this.loadDate();
    this.loadCategory();

}
}
</script>

<style scoped>

</style>