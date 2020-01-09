<template>
    <div>
        <el-button type="success" size="small" @click="toAddHendler">添加</el-button>
        <el-button type="danger" size="small">批量删除</el-button>
        <!-- 表格 -->
        <el-table :data="comment">
            <el-table-column prop="id" label="编号"></el-table-column>
            <el-table-column prop="content" label="内容"></el-table-column>
            <el-table-column prop="commentTime" label="评论时间"></el-table-column>
            <el-table-column prop="ordId" label="描述"></el-table-column>
            
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
        <el-dialog  :title="title" :visible.sync="visible" width="60%">
            {{form}}
            <el-form :model="form" labei-width="80px"> 
               <el-form-item label="编号">       
                 <el-input v-model="form.id"></el-input>
               </el-form-item>
               <el-form-item label="内容">       
                 <el-input v-model="form.content"></el-input>
               </el-form-item>
               <el-form-item label="评论时间">       
                 <el-input v-model="form.commentTime"></el-input>
               </el-form-item>
               <el-form-item label="描述">       
                 <el-input v-model="form.oldId"></el-input>
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
    methods:{
        loadData(){
            let url="http://localhost:6677/comment/findAll"
            request.get(url).then((response)=>{
               this.comment=response.data;
            })
            
        },

        submitHandler(){
            // this.form 对象--字符串-->后台
            // 通过request与后台交互，并且携带参数
            let url="http://localhost:6677/comment/saveOrUpdate";
            request({
                url,
                method:"POST",
                headers:{
                    "Content-Type":"application/x-www-form-urlencoded"
                },
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
        toAddHendler(){
            this.form={
                type:"comment"
            }
            this.title="录入评论信息";
            this.visible=true;
        },
        toDeleteHandler(id){
           this.$confirm('此操作将永久删除该文件, 是否继续?', '提示', {
            confirmButtonText: '确定',
            cancelButtonText: '取消',
            type: 'warning'
            }).then(() => {
                //   调用后台接口完成删除
                let url="http://localhost:6677/comment/deleteById?id="+id;
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
            this.title="修改评论信息";
            this.visible=true;
        },
        closeModalHandler(){
            this.visible=false;
        },



    },
    data(){
        return{
           visible:false,
           comment:[{
              
           }],
           form:{
               type:"comment"
           },
           title:'测试'
           

        }    
    },
    created(){
        this.loadData();
    }
}


</script>


<style scoped>
