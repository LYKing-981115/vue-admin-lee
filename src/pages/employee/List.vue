<template>
    <div>
        <!-- 按钮 -->
        <el-button type="primary" size="small" @click="toAddHandler">添加</el-button>
        <el-button type="danger" size="small">批量删除</el-button>
        <!-- 按钮结束 -->
        <!-- 表格 -->
        <el-table :data="employees">
            <el-table-column fixed="left" prop="id" label="编号"></el-table-column>
            <el-table-column fixed="left" prop="username" label="用户名"></el-table-column>
            <el-table-column prop="realname" label="真实姓名"></el-table-column>
            <el-table-column prop="gender" label="姓别"></el-table-column>
            <el-table-column width="120" prop="telephone" label="手机号"></el-table-column>
            <el-table-column width="200" prop="idCard" label="身份证号"></el-table-column>
            <el-table-column width="200" prop="bankCard" label="银行卡号"></el-table-column>
            <el-table-column fixed="right" prop="" label="操作">
                 <template v-slot="slot"><!-- 获取当前行数据 -->
                    <a href="" @click.prevent="toDeleteHandler(slot.row.id)">删除</a>
                    <a href="" @click.prevent="toUpdateHandler(slot.row)">修改</a>
                </template>   
            </el-table-column>
                
        </el-table>
        <!-- 表格结束 -->
        <!-- 分页 -->
        <el-pagination
            layout="prev, pager, next"
            :total="50">
         </el-pagination>
         <!-- 分页结束 -->
         <!-- 模态框 -->
         
          <el-dialog
        :title="title"
        :visible.sync="visible"
        width="50%"
        >
        --{{form}}
        <el-form label-width="80px">
            <el-form-item label="用户名">
                <el-input v-model="form.username"/>
            </el-form-item>
             <el-form-item  label="密码">
                <el-input v-model="form.password"/>
            </el-form-item>
             <el-form-item label="姓名">
                <el-input v-model="form.realname"/>
            </el-form-item>
            <el-form-item label="性别">
            <el-radio-group v-model="form.gender">
                 <el-radio label="男" >男</el-radio>
                 <el-radio label="女">女</el-radio>
            </el-radio-group>
            </el-form-item>
             <el-form-item label="手机号">
                <el-input v-model="form.telephone"/>
            </el-form-item>
             <el-form-item label="身份证号">
                <el-input v-model="form.idCard"/>
            </el-form-item>
             <el-form-item label="银行卡号">
                <el-input v-model="form.bankCard"/>
            </el-form-item>
        </el-form>
        <span slot="footer" class="dialog-footer">
            <el-button  size="small" @click="closeModalHandler">取 消</el-button>
            <el-button type="primary" @click="submitHandler" size="small">确 定</el-button>
        </span>
        </el-dialog>
        <!-- 模态框结束 -->
    </div>    
</template>

<script>
import request from '@/utils/request'
import querystring from 'querystring'
export default {
    data(){
        return{
            visible:false,
            title:"录入员工信息",
            employees:[],
            form:{
                type:"waiter"
            }
        }
    },
    created(){
        //在页面加载出来的时候加载数据
        this.loadData();
    },
    methods:{
        submitHandler(){
            let url = "http://localhost:6677/waiter/saveOrUpdate"
            //前端向后台发送请求，完成数据的保存操作
            request({
                url,
                method:"POST",
                headers:{
                    "Content-Type":"application/x-www-form-urlencoded"//告诉后台我是查询字符串
                },
                data:querystring.stringify(this.form)//将对象(this.form)转为查询字符串并发送给后台
            }).then((response)=>{
             //模态框关闭
                this.closeModalHandler();//方法中可以调用方法，记得加this
                //刷新数据
                this.loadData();
                //提示消息
                this.$message({
                    type:"success",
                    message:response.message
                    }) 
            })
        },
        //this指向vue实例，通过vue实例可以访问该实例中的数据，method中的返回方法
        //重载员工数据
        loadData(){
        let url="http://localhost:6677/waiter/findAll"
        request.get(url).then((response)=>{//箭头函数中的this指向外部函数中的this,response是完成时要执行的函数（必写）
        this.employees = response.data;
        })
        },
        toAddHandler(){
            this.title="录入员工信息"
            this.visible=true;
        },
        closeModalHandler(){
            this.visible=false;
        },
        toDeleteHandler(id){
        this.$confirm('此操作将永久删除该文件, 是否继续?', '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {
          this.$message({
            type: 'success',
            message: '删除成功!'
          });
        })
        },
        toUpdateHandler(row){
            this.title="修改员工信息"
            this.visible=true
        }
    }
}
</script>

<style scoped>

</style>