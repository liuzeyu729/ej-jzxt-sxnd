<template>
    <div>
        <!-- 按钮 -->
        <el-button type="success" size="small" @click="toAddHandler">添加</el-button>
        <el-button type="danger" size="small">批量删除</el-button>
        <!-- 表格 -->
        <el-table :data="employee">
         <el-table-column prop="id" label="编号"></el-table-column>
         <el-table-column prop="username" label="用户名"></el-table-column>    
         <el-table-column prop="realname" label="姓名"></el-table-column>    
         <el-table-column prop="gender" label="性别"></el-table-column>
         <el-table-column width=200 prop="idCard" label="身份证"></el-table-column>
         <el-table-column width=200 prop="bankCard" label="银行卡"></el-table-column>
         <el-table-column width=120 prop="telephone" label="手机号"></el-table-column>
          <el-table-column fixed="right" label="操作">
             <template v-slot="slot">
                 <a href="" @click.prevent="toDeleteHandler(slot.row.id)">删除</a>
                 <a href="" @click.prevent="toUpdateHandler(slot.row)">修改</a>
             </template>
         </el-table-column>
        </el-table>
        <!-- 分页 -->
        <el-pagination layout="prev, pager, next" :total="50"></el-pagination>

        <!-- 模态框 -->
        <el-dialog :title="title"  :visible.sync="visible" width="60%"> 
            {{form}}
            <el-form :model="form" label-width="80px">
                <el-form-item label="用户名">
                    <el-input v-model="form.username"></el-input>
                </el-form-item>
                <el-form-item label="密码">
                    <el-input v-model="form.password"></el-input>
                </el-form-item>
                <el-form-item label="姓名">
                    <el-input v-model="form.realname"></el-input>
                </el-form-item>
                <el-form-item label="性别">
                    <el-radio-group v-model="form.gender">
                        <el-radio label="男">男</el-radio>
                        <el-radio label="女">女</el-radio>
                </el-radio-group>
                </el-form-item>
                <el-form-item label="手机号">
                    <el-input v-model="form.telephone"></el-input>
                </el-form-item>
                <el-form-item label="身份证号">
                    <el-input v-model="form.idCard"></el-input>
                </el-form-item>
                <el-form-item label="银行卡号">
                    <el-input v-model="form.bankCard"></el-input>
                </el-form-item>
            </el-form>


            <span slot="footer" class="dialog-footer">
                <el-button size="samll" @click="closeModalHandler">取 消</el-button>
                <el-button size="samll" type="primary" @click="submitHandler">确 定</el-button>
            </span>
        </el-dialog>
    </div>
</template>


<script>
import request from '@/utils/request'
import querystring from 'querystring'
export default {
    // 存放调用的方法
    methods:{
        submitHandler(){
            let url="http://localhost:6677/waiter/saveOrUpdate";
            // 前端向后台发送请求，完成数据的保存操作
            request({
                url,
                method:"post",
                // 告诉给后台我的请求体中放的是查询字符串
                headers:{
                  "Content-Type":"application/x-www-form-urlencoded"
                },
            // 请求体中的数据，将this.form转换为查询字符串发送给后台
                data:querystring.stringify(this.form)
            }).then((response)=>{
                // 模态框关闭
                this.closeModalHandler();

                this.loadData();
                // 提示消息
                this.$message({
                    type:"success",
                    message:response.message

                })

            })
        },
        loadData(){
           let url="http://localhost:6677/waiter/findAll"
            request.get(url).then((response)=>{
           //   将查询结果设置到customers中,this指向外部函数this
            this.employee=response.data;
            })
        },
         
        toDeleteHandler(id){
        //  确认
           this.$confirm('此操作将永久删除该文件, 是否继续?', '提示', {
            confirmButtonText: '确定',
            cancelButtonText: '取消',
            type: 'warning'
           }).then(() => {
                //   调用后台接口完成删除
                let url="http://localhost:6677/customer/deleteById?id="+id;
                request.get(url).then((response)=>{
                    // 刷新数据
                    this.loadData();
                    // 提示结果
                    this.$message({
                        type: 'success',
                        message: '删除成功!'+id
                    });
                })

            
           })
        },
        toUpdateHandler(row){
            this.form=row;
            this.visible=true;
            this.title="修改员工信息";
        },
        closeModalHandler(){
            this.visible=false;
        },
        toAddHandler(){
            this.form={
               type:"employee"
            }
            this.title="录入员工信息";
            this.visible=true;
        }
    },
    //存放想要的数据
    data(){
        return{
            
            visible:false,
            employee:[],
            form:{
                type:"waiter"
            },
            title:"ss"           
        }
    },
    created(){
        // 在页面加载出来的时候加载数据
        this.loadData();
    }
}
</script>


<style scoped>

</style>