<template>
    <div>
        <h2>栏目管理</h2>
        <!-- 按钮 -->
        <el-button size="small" type="primary" @click="toAddHandel">添加</el-button>
        <el-button size="small" type="danger">批量删除</el-button>
        <!-- 表格 -->
        <el-table :data="employees">
            <el-table-column prop="id" label="编号"></el-table-column>
            <el-table-column prop="name" label="栏目名称"></el-table-column>
            <el-table-column prop="num" label="序号"></el-table-column>
            <el-table-column prop="parentId" label="父栏目"></el-table-column>
            <el-table-column fixed="right" label="操作">
             <!-- slot用来获取当前行的数据 -->
            <template v-slot="slot">
                <a href="" @click.prevent="toDeleteHandler(slot.row.id)">删除</a>
                <a href="" @click.prevent="toUpdateHandler(slot.row)">修改</a>
            </template>
            </el-table-column>
            </el-table>
        <!-- 分页 -->
        <el-pagination
        layout="prev, pager, next"
        :total="50">
        </el-pagination>
        <!-- 模态框 -->
        <el-dialog
            :title="title"
            :visible.sync="visible"
            width="60%"
        >
        测试{{form}}
        <el-form :model="form" label-width="80px">
            <el-form-item label="编号">
                <el-input v-model="form.id"></el-input>
            </el-form-item>
             <el-form-item label="栏目名称">
                <el-input v-model="form.name"></el-input>
            </el-form-item>
             <el-form-item label="序号">
                <el-input v-model="form.num"></el-input>
            </el-form-item>
            <el-form-item label="父栏目">
                <el-input v-model="form.parentId"></el-input>
            </el-form-item>
        </el-form>

        <span slot="footer" class="dialog-footer">
            <el-button @click="closeModalHandler" size="small">取 消</el-button>
            <el-button type="primary" @click="submitHandel" size="small">确 定</el-button>
        </span>
        </el-dialog>
    </div>
</template>

<script>
import request from'@/utils/request' //自定义库
import querystring from 'querystring' //系统库
export default {
    data(){
        return{
        title:"录入栏目信息",
        visible:false,
        employees:[],
        form:{
            type:"waiter"
        }
        }  
    },
    created(){
        // 在页面加载出来的时候加载数据
        this.loadData()
    },
    methods:{
         submitHandel(){
          // this.form  对象->字符串->后台
          // 调用后台借口，并且携带参数
           let url = "http://localhost:6677/category/saveOrUpdate"
           //前段向后套发送请求，完成数据的保存操作
           request({
           url,
           method:"POST",
           headers:{
           "Content-Type":"application/x-www-form-urlencoded"
                 },
            data:querystring.stringify(this.form)
            // 将this.form转换为字符串
            }).then((response)=>{
              // 模态框关闭
              this.closeModalHandler();
              //刷新
              this.loadData();
              // 提示消息
              this.$message({
              type:"success",
              message:response.message
              })
          })

        },
    loadData(){
        let url = "http://localhost:6677/category/findAll"
        request.get(url).then((response)=>{
            //箭头函数中的this只想外部函数中的this
            this.employees = response.data;
        })
    },
    toAddHandel(){
        this.title="录入栏目信息";
        this.visible = true;
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
        this.title="修改栏目信息";
        this.visible = true;

    },
    closeModalHandler(){
        this.visible = false;
    }
}
}
</script>
<style scoped>

</style>