<template>
<div>
    <el-button type="success" size="small" @click="toAddHandler">添加</el-button>
    <el-button type="danger" size="small">批量删除</el-button>
<el-table :data="address">
    <el-table-column prop="id" label="编号"></el-table-column>
    <el-table-column prop="province" label="省份"></el-table-column>
    <el-table-column prop="city" label="城市"></el-table-column>
    <el-table-column prop="address" label="详细地址"></el-table-column>
    <el-table-column prop="telephone" label="联系方式"></el-table-column>
    <el-table-column prop="customerId" label="顾客名称"></el-table-column>
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
        <el-form-item label="省份">
          <el-input type="province" v-model="form.province"></el-input> 
        </el-form-item>
        <el-form-item label="联系方式">
          <el-input type="telephone" v-model="form.telephone"></el-input> 
        </el-form-item>
        <el-form-item label="城市">
          <el-input type="city" v-model="form.city"></el-input> 
        </el-form-item>
        <el-form-item label="详细地址">
         <el-input type="address" v-model="form.address"></el-input> 
        </el-form-item>
        <el-form-item label="顾客名称">
          <el-input type="customerId" v-model="form.customerId"></el-input> 
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
        let url = "http://134.175.154.93:6677/address/findAll"
                request.get(url).then((response)=>{
                    //将查询结果放置到address中,then()中使用“=>”保证this指向外部函数的this。👆
                    this.address=response.data;
                })
      },
      //this.form对象---字符串--->后台
      //通过request有后台进行交互，并且要携带参数
        submitHandler(){
            let url="http://134.175.154.93:6677/address/saveOrUpdate";
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
          let url="http://134.175.154.93:6677/address/deleteById?id="+id;
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
            this.title="修改地址信息";
            this.visible=true;
        },
        closeModalHandler(){
            this.visible=false;
        },    
        toAddHandler(){
            this.form={
              type:"address"
            }
            this.visible=true;
            this.title="录入地址信息";
        }
    },
//用于存放要向网页中显示的信息
    data(){
        return{
            title:"添加顾客信息",
            visible:false,
            address:[],
            form:{
              type:"address"
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