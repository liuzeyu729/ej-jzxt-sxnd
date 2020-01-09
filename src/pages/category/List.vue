<template>
<div>
    <el-button type="success" size="small" @click="toAddHandler">添加</el-button>
    <el-button type="danger" size="small">批量删除</el-button>
<el-table :data="category">
    <el-table-column prop="id" label="编号"></el-table-column>
    <el-table-column prop="name" label="名称"></el-table-column>
    <el-table-column prop="num" label="数字"></el-table-column>
    <el-table-column prop="icon" label="图标">
    <template   slot-scope="scope">            
                    <img :src="scope.row.icon"  min-width="70" height="70" />
                 </template> </el-table-column>
    <el-table-column prop="parentId" label="父栏"></el-table-column>
    
    <el-table-column label="操作">
        <template v-slot="slot"> <!--solt了解一下-->
            <a href="" @click.prevent="toDeleteHandler(slot.row.id)">删除</a>
            <a href="" @click.prevent="toUpdateHandler(slot.row)">修改</a>
        </template>
    </el-table-column>
</el-table>
 <!--分页-->
<el-pagination
    layout="prev, pager, next"
    :total="1000">
  </el-pagination>
  <!--分页结束-->
   <!--模态框-->
  <el-dialog
  :title="title"
  :visible.sync="visible"
  width="30%">
  {{form}}
  <el-form :model="form" label-width="80px">
        <el-form-item label="用户名">
          <el-input v-model="form.username"></el-input>
        </el-form-item>
        <el-form-item label="密码">
          <el-input type="password" v-model="form.password"></el-input> 
        </el-form-item>
        <el-form-item label="编号">
          <el-input type="id" v-model="form.id"></el-input> 
        </el-form-item>
        <el-form-item label="名称">
          <el-input type="name" v-model="form.name"></el-input> 
        </el-form-item>
        <el-form-item label="图标">
          <el-input type="icon" v-model="form.icon"></el-input> 
        </el-form-item>
        <el-form-item label="父栏">
          <el-input type="parentId" v-model="form.parentId"></el-input> 
        </el-form-item>
        <el-form-item label="数字">
         <el-input type="num" v-model="form.num"></el-input> 
        </el-form-item>     
  </el-form>
  <span slot="footer" class="dialog-footer">
    <el-button size="small" @click="closeModalHandler">取 消</el-button>
    <el-button type="primary" size="small" @click="submitHandler">确 定</el-button>
  </span>
</el-dialog>
<!--模态框结束-->
</div>
</template>
<script>
import querystring from 'querystring'
import request from '@/utils/request'
export default {
//用于存放网页中需要调用的方法
    methods:{
      loaddata(){
        let url = "http://localhost:6677/category/findAll"
                request.get(url).then((response)=>{
                    //将查询结果放置到category中,then()中使用“=>”保证this指向外部函数的this。👆
                    this.category=response.data;
                })
      },
      //this.form对象---字符串--->后台
      //通过request有后台进行交互，并且要携带参数
        submitHandler(){
            let url="http://localhost:6677/category/saveOrUpdate";
            request({
              url,
              method:"POST",
              headers:{
                "Content-Type":"application/x-www-form-urlencoded"
              },
              data:querystring.stringify(this.form)

            }).then((response)=>{
            //请求结束
            this.closeModalHandler();
            this.loaddata();
            this.$message({
              type:"success",
              message:response.message
            })
            })
        },
        toDeleteHandler(id){
          //调用后台接口完成删除操作
             this.$confirm('此操作将永久删除该文件, 是否继续?', '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {
          let url="http://localhost:6677/category/deleteById?id="+id;
          request.get(url).then((response)=>{
                  this.loaddata();
                  this.$message({
                    type: 'success',
                    message: '删除成功!'
          });
          })
          })
        },
        toUpdateHandler(row){
          //在模态框的表中显示当前行信息
            this.form=row;
            this.title="修改信息";
            this.visible=true;
        },
        closeModalHandler(){
            this.visible=false;
        },    
        toAddHandler(){
            this.form={
              type:"category"
            }
            this.visible=true;
            this.title="录入信息";
        }
    },
//用于存放要向网页中显示的信息
    data(){
        return{
            title:"添加信息",
            visible:false,
            category:[],
            form:{
              type:"category"
            }
            }
    },
    created(){
        this.loaddata();
    }
}
</script>
<style scoped>

</style>