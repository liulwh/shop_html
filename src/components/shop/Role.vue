<template>
  <div>
    <h3>角色信息</h3>

        <el-button type="success" @click="addFormFlag=true">新增</el-button>

    <el-table
      :data="tableData"
      style="width: 100%"
      @row-click="getDetails">

      <el-table-column
        prop="id"
        label="序号"
        width="180">
      </el-table-column>

      <el-table-column
        prop="name"
        label="角色名称"
        width="180">
      </el-table-column>






      <!--<el-table-column
        prop="ord"
        label="汽车品牌">
      </el-table-column>-->



      <el-table-column
        prop="createDate"
        label="上架时间">
      </el-table-column>


      <el-table-column
        prop="author"
        label="操作人">
      </el-table-column>


      <el-table-column
        fixed="right"
        label="操作"
        width="100">
        <template slot-scope="scope">
          <el-button type="text" size="small"@click="() => updateFormFlag=true">编辑</el-button>
          <el-button type="text" size="small" v-on:click="deleteBrand(scope.row.id)">删除</el-button>
        </template>
      </el-table-column>

    </el-table>

    <el-pagination

      @current-change="handleCurrentChange"
      @size-change="handleSizeChange"
      :page-sizes="sizes"
      :page-size="size"
      layout="total, sizes, prev, pager, next, jumper"
      :total="count">
    </el-pagination>


    <!--  新增的弹框   -->
    <el-dialog title="录入角色信息" :visible.sync="addFormFlag">

      <el-form :model="addRoleForm"  label-width="80px">

        <el-form-item label="角色名称" prop="name">
          <el-input v-model="addRoleForm.name" autocomplete="off" ></el-input>
        </el-form-item>



      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button @click="addRoleForm = false">取 消</el-button>
        <el-button type="primary" @click="addForm">确 定</el-button>
      </div>
    </el-dialog>



    <!--  修改的弹框   -->
    <el-dialog title="修改品牌信息" :visible.sync="updateFormFlag">

      <el-form :model="updateRoleForm" ref="updateBrandForm"  label-width="80px">

        <el-form-item label="id" prop="id">
          <el-input v-model="updateRoleForm.id" autocomplete="off" ></el-input>
        </el-form-item>

        <el-form-item label="角色名称" prop="name">
          <el-input v-model="updateRoleForm.name" autocomplete="off" ></el-input>
        </el-form-item>

      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button @click="updateFormFlag = false">取 消</el-button>
        <el-button type="primary" @click="updateForm">确 定</el-button>
      </div>
    </el-dialog>





  </div>
</template>

<script>
  import  ajax from 'axios'
  import qs from 'qs'

    export default {
        name: "Role",
      data(){
        return{
          formInline:{
            start:1,
            size:8,
            name:'',
          },
          updateFormFlag:false,
          updateRoleForm:{
            name:"",
          },
          tableData: [],
          addFormFlag:false,
          addRoleForm:{
            name:"",
          },
          roleData:[],
          count:0,
          sizes:[2,3,5,10],
          size:2,
          start:1
        }
      },methods:{
        getDetails(row){
          //alert(row)
          var athis = this;
          athis.updateRoleForm.id=row.id;
          athis.updateRoleForm.name=row.name;

          console.log(row.imgpath);
          console.log(row)//此时就能拿到整行的信息
        },
        updateForm:function (rs) {
          console.log("ssss"+rs);
          var a =this;
          ajax.post("http://localhost:8080/api/role/updateRole",qs.stringify(this.updateRoleForm)).then(res=>{
            this.updateFormFlag=false;
            a.queryData(1);

          }).catch(err=>console.log(err))

        },
        queryData:function () {
          var formInline=qs.stringify(this.formInline);
          var athis=this;
          console.log(athis);
          ajax.get("http://localhost:8080/api/role/getRoleDate?start="+athis.start+"&size="+athis.size).then(function (res) {
            athis.tableData=res.data.data.list;
            athis.roleData=res.data.data.list;
            athis.count=res.data.data.count;
            console.log(res);
          }).catch(function () {
          })
        },
        addForm:function () {
          var athis=this;
          ajax.post("http://localhost:8080/api/role/addRole",qs.stringify(this.addRoleForm)).then(function () {
            athis.addFormFlag = false;
            history.go(0);
          }).catch(function () {

          })
        },deleteBrand:function (id) {
          ajax.delete("http://localhost:8080/api/role/delRole?id="+id).then(function () {
            history.go(0);
          }).catch(function () {
          })

        },handleCurrentChange:function(start){ //跳转页面
          console.log(start);
          this.start=start;
          this.queryData(start);
        },handleSizeChange:function(size){ //跳转页面
          this.size=size;
          this.queryData(1);
        }
      },created:function () {
        //请求数据
        this.queryData(1,2);
        //查询品牌数据
      }
    }
</script>

<style scoped>

</style>
