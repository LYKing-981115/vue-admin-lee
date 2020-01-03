<template>
    <div>
        <!--按钮 -->
        <el-button size="small" type="info" @click="toAddHandler">添加</el-button>
        <el-button type="danger" size="small">批量删除</el-button>
        <!--表格 -->
        <el-table :data="customers">
            <el-table-column prop="id" label="编号"></el-table-column>
            <el-table-column prop="realname" label="姓名"></el-table-column>
            <el-table-column prop="telephone" label="联系方式"></el-table-column>
            <el-table-column label="操作">
                <template v-slot="slot">
                    <a href="" @click.prevent="toDeleteHandler(slot.row.id)">删除</a>
                    <a href="" @click.prevent="toUpdateHandler(slot.row)">修改</a>
                </template>

            </el-table-column>

        </el-table>
        <!--表格结束 -->
        <!--分页开始 -->
        <!-- <el-pagination
            layout="prev, pager, next"
            :total="50">
         </el-pagination> -->
        <!--分页结束 -->
        <!--模态框 -->
        <el-dialog
        :title="title"
        :visible.sync="visible"
        width="50%">
        ---{{form}}
        <el-form :model="form" label-width="80px">
            <el-form-item label="用户名">
                <el-input v-model="form.username"></el-input>
            </el-form-item>
            <el-form-item label="密码">
                <el-input type=password v-model="form.password"></el-input>
            </el-form-item>
             <el-form-item label="真实姓名">
                <el-input v-model="form.realname"></el-input>
            </el-form-item>
             <el-form-item label="手机号">
                <el-input v-model="form.telephone"></el-input>
            </el-form-item>
        </el-form>
        <span slot="footer" class="dialog-footer">
            <el-button  size="small" @click="closeModalHandler">取 消</el-button>
            <el-button type="primary" @click="submitHandler" size="small">确 定</el-button>
        </span>
        </el-dialog>
    </div>    
</template>

<script>//写数据
import request from '@/utils/request'//@=src目录
import querystring from 'querystring'
export default {
    //用于存放网页中需要调用的方法
    methods:{
        loadData(){
        let url = "http://localhost:6677/customer/findAll"
        request.get(url).then((response)=>{
            //将查询结果设置到customers中。this指向外部环境的this
            this.customers = response.data;})
        },
        submitHandler(){
            //通过request与后台交互，并且要携带参数
            let url="http://localhost:6677/customer/saveOrUpdate"
            request({//request方法传递了一个对象参数
                url,
                method:"POST",
                headers:{
                    "Content-Type":"application/x-www-form-urlencoded"//告诉后台我是查询字符串
                },
                data:querystring.stringify(this.form)//将对象转为查询字符串
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
        toAddHandler(){
            this.form={
                type:"customer"
            }
            this.title="录入顾客信息"
            this.visible=true;
        },
        closeModalHandler(){
            this.visible=false;
        },
        toUpdateHandler(row){
            //模态框表单中显示当前行所有的信息
            this.form=row;
            this.visible=true;
            this.title="修改顾客信息"
        },
        toDeleteHandler(id){
            //先确认
        this.$confirm('此操作将永久删除该文件, 是否继续?', '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {
            //调用后台接口完成删除操作
            let url="http://localhost:6677/customer/deleteById?id="+id
            request.get(url).then((response)=>{
                //刷新数据
                this.loadData();
                //提示结果
                this.$message({
                type: 'success',
                message:response.message
            });
            })
          
        })
        }
    },
    //用于存放要向网页中存放的数据
    data(){
        return{
            title:"录入顾客信息",
            visible:false,
            customers:[],
            form:{
                type:"customer"
            }
        }//返回对象
    },
    created(){
        //this为当前vue实例
        //vue实例创建完毕执行的操作
       this.loadData();
        
    }
}
</script>

<style scoped>

</style>