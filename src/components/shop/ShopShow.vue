<template>

  <div>
      <h1>商品信息</h1>

    <el-table
      :data="tableData"
      style="width: 100%"
     >
<!--  @row-click="getDetails" -->
      <el-table-column
        prop="id"
        label="序号"
        width="180">
      </el-table-column>

      <el-table-column
        prop="name"
        label="商品名称"
        width="180">
      </el-table-column>

      <el-table-column
        prop="title"
        label="商品标题">
      </el-table-column>

      <el-table-column
        prop="imgPath"
        label="商品图片">
        <template slot-scope="scope">
          <img width="50px" :src="scope.row.imgpath"/>
        </template>
      </el-table-column>

      <el-table-column
        prop="productdecs"
        label="商品介绍">
      </el-table-column>


      <el-table-column
        prop="brandName"
        label="品牌">
      </el-table-column>

      <el-table-column
        prop="price"
        label="价格">
      </el-table-column>


      <el-table-column
        prop="stocks"
        label="库存">
      </el-table-column>


      <el-table-column
        prop="typeName"
        label="分类">
      </el-table-column>

      <el-table-column
        fixed="right"
        label="操作"
        width="100">
       <template slot-scope="scope">
          <el-button type="text" size="small" v-on:click="deleteShop(scope.row.id)">删除</el-button>
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
        name: "ShopShow",
      data(){
          return {
            tableData:[],
            count:0,
            sizes:[10,15,20,25],
            size:10,
            start:1
          }
      },methods:{
          queryData:function () {
            var athis=this;
            this.$ajax.get("http://localhost:8080/api/shop/queryShop?start="+this.start+"&size="+this.size).then(function (res) {
              athis.tableData=res.data.data.data;
              athis.count=res.data.data.count;
              console.log(athis.tableData);

            }).catch(function () {

              alert("查询商品信息失败");
            })

          }
          ,deleteShop:function (id) {
            var athis=this;
          this.$ajax.delete("http://localhost:8080/api/shop/deleteShop?id="+id).then(function () {
            alert("删除成功");
            athis.queryData();
          }).catch(function () {
            alert("删除失败");
          })

        }
          ,handleCurrentChange:function(start){ //跳转页面
          console.log(start);
          this.start=start;
          this.queryData(start);
        }
        ,handleSizeChange:function(size){ //跳转页面
          this.size=size;
          this.queryData(1);
        }

      },created:function () {

          this.queryData();
      }
    }
</script>

<style scoped>

</style>
