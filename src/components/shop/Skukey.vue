<template>
    <div>
      <h1>属性</h1>

      <el-table
        :data="tableData"
        style="width: 100%"
       >

        <el-table-column
          prop="id"
          label="序号"
          width="180">
        </el-table-column>

        <el-table-column
          prop="name"
          label="名称"
          width="180">
        </el-table-column>

        <el-table-column
          prop="nameCH"
          label="中文名称">
        </el-table-column>



        <el-table-column
          prop="typeId"
          label="品牌id">
        </el-table-column>

        <!--<el-table-column
          prop="ord"
          label="汽车品牌">
        </el-table-column>-->

        <el-table-column
          prop="type"
          label="属性的类型">
        </el-table-column>

       <!-- <el-table-item label="特殊资源">
          <el-radio-group  size="medium">
            <el-radio border label="线上品牌商赞助"></el-radio>
            <el-radio border label="线下场地免费"></el-radio>
          </el-radio-group>
        </el-table-item>-->

        <el-table-column
          prop="createDate"
          label="上架时间">
        </el-table-column>

        <el-table-column
          prop="updateDate"
          label="下架时间">
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
            <!--<el-button type="text" size="small"@click="() => updateFormFlag=true">编辑</el-button>-->
            <el-button type="text" size="small" v-on:click="deleteSku(scope.row.id)">删除</el-button>
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


    </div>
</template>

<script>
    export default {
        name: "Skukey",
      data(){
          return{
            tableData:[],
            sizes:[2,3,5,10],
            size:2,
            start:1,
            count:0
          }
      },
      methods:{
        queryData:function () {
          var athis=this;
          this.$ajax.get("http://localhost:8080/api/skukey/querySkuKeyData?start="+this.start+"&size="+this.size).then(function (res) {
            athis.tableData=res.data.data.list;
            athis.count=res.data.data.count;
            console.log(res.data.data.list);

          }).catch(function () {

            alert("查询失败")
          })
        },deleteSku:function (id) {

          this.$ajax.delete("http://localhost:8080/api/skukey/delSkuKey?id="+id).then(function () {
            alert("删除成功");
            history.go(0);
          }).catch(function () {

          })
        }
        ,handleCurrentChange:function(start){ //跳转页面
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
