<template>
    <div>
        <el-radio v-model="radio" label=""></el-radio>
       <el-button type="primary" size="small" @click="toAddHandler">添加</el-button>
       <el-button type="danger" size="small">批量删除</el-button>
       
       <el-table :data="products">
           <el-table-column fixed="left" prop="id" label="编号"></el-table-column>
            <el-table-column fixed="left" prop="name" label="产品名称"></el-table-column>
           <el-table-column prop="price" label="价格"></el-table-column>
           <el-table-column prop="decripation" label="描述"></el-table-column>
           <el-table-column width=120px prop="categoryId" label="所属产品"></el-table-column>
        <el-table-column label="操作">
        <template v-slot="slot">
          <a href="" @click.prevent="toDeleteHandler(slot.row.id)">
           meta: { 
                icon:'nested' }
          </a>
          <a href="" @click.prevent="toUpdateHandler"> </a>
        </template>
      </el-table-column>
       </el-table>  
        <!--分页符-->
       <el-pagination
          layout="prev, pager, next"
           :total="50">
        </el-pagination> 
        <!--/分页符-->
              <!--模态框-->
        <el-dialog
            title="录入产品信息"
            :visible.sync="visible"
            width="60%"> 
            ---{{form}}
            <el-form :model=from label-width="80px">
                <el-form-item label="产品名称">
                    <el-input v-model="form.name"/>
                </el-form-item>
                <el-form-item label="价格">
                    <el-input  v-model="form.price"/>
                </el-form-item>
                <el-form-item label="描述">
                    <el-input v-model="form.description"/>
                </el-form-item>
                <el-form-item label="所属产品">
                     <el-input v-model="form.categoryId"/>
                </el-form-item>       
            </el-form>
            <span slot="footer" class="dialog-footer">
                <el-button @click="closModleHandler" >取 消</el-button>
                <el-button type="primary" @click="closModleHandler" >确 定</el-button>
            </span>
        </el-dialog>
        <!--/模态框-->
    </div>
</template>

<script>
import request from '@/utils/request'
import querystring from 'querystring'
export default {
    methods:{
        submitHandler(){
            let url = "http://134.175.154.93:6677/product/saveOrUpdate"
            request({
                url,
                method:"POST",
                headers:{
                    "Conten-Type":"application/x-form-urlencoded"
                },
                data:querystring.stringfy(this.form)
            })
        },
        loadData(){
            let url = "http://134.175.154.93:6677/product/findAll";
            request.get(url).then((response)=>{
                this.products = response.data;
            }).then((response)=>{
                // 模态框关闭
              this.closeModalHandler();
                 // 刷新
              this.loadData();
                 // 提示消息
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
             this.$message({
                type: 'success',
                message: '删除成功!'
             });
            })
        },
        toUpdateHandler(){
            this.visible = true;
        },
       closModleHandler(){
           this.visible = false;
       },
        toAddHandler(){
            this.title = "录入产品信息";
            this.visible = true ;
        }
    },
    data(){
        return {
            title:"录入产品信息",
            visible:false,
            products:[],
            form:{
                type:"product"
            }
        }  
    },
    created(){
        //在页面加载出来的时候加载数据
        this.loadData();
    }
}
</script>
<style scoped>
</style>
