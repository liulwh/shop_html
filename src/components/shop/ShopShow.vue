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
          <img width="50px" :src="scope.row.imgPath"/>
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
          <el-button type="text" size="small" v-on:click="updateShop(scope.row.id)">修改</el-button>
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


    <el-dialog title="修改品牌信息" :visible.sync="updateFormFlag">
    <el-form v-model="addShop" ref="addAttrForm"  label-width="80px">

      <el-form-item style="width:400px" label="商品名称" prop="name">
        <el-input v-model="addShop.name" autocomplete="off" ></el-input>
      </el-form-item>

      <el-form-item align="center" style="width:400px" label="商品标题" prop="nameCH">
        <el-input v-model="addShop.title"></el-input>
      </el-form-item>



      <el-form-item align="center" label="品牌分类" prop="brandId">
        <el-select v-model="addShop.brandId" placeholder="请选择">
          <el-option v-for="item in Pinpais" :key="item.id" :label="item.name" :value="item.id"></el-option>
        </el-select>
      </el-form-item>


      <el-form-item label="图片">
        <el-upload
          class="upload-demo"
          action="http://localhost:8080/uploadFile/uploadFile"
          :on-success="imgCallBack"
          name="file"
          list-type="picture">
          <el-button size="small" type="primary">点击上传</el-button>
          <div slot="tip" class="el-upload__tip">不超过500kb</div>
          <img width="50px" :src="addShop.imgPath"/>
        </el-upload>
      </el-form-item>
      <el-form-item style="width:400px"  label="商品介绍" prop="productdecs">
        <el-input type="textarea" v-model="addShop.productdecs"></el-input>
      </el-form-item>
      <el-form-item align="center" style="width:400px" label="价格" prop="price">
        <el-input v-model="addShop.price"></el-input>
      </el-form-item>
      <el-form-item align="center" style="width:400px" label="库存" prop="stocks">
        <el-input v-model="addShop.stocks"></el-input>
      </el-form-item>
      <el-form-item align="center" style="width:400px" label="排序" prop="sortNum">
        <el-input v-model="addShop.sortNum"></el-input>
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

    export default {
        name: "ShopShow",
      data(){
          return {
            tableData:[],
            count:0,
            sizes:[10,15,20,25],
            size:10,
            start:1,
            // 修改数据
            updateFormFlag:false,
            addShop:{},

            Pinpais:[],
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
        , updateShop:function (id) {
            this.updateFormFlag=true;
            var athis=this;
          alert(id);
          this.$ajax.get("http://localhost:8080/api/shop/queryShopById?id="+id).then(function (res) {

            athis.addShop=res.data.data.data;

            console.log(athis.addShop);
          }).catch(function () {
            alert("回显失败");
          })

        },
        updateForm:function(){
            var athis=this;
            //alert(athis.addShop);
            debugger;
          this.$ajax.post("http://localhost:8080/api/shop/updateShop",this.$qs.stringify(athis.addShop)).then(function (res) {
            athis.updateFormFlag=false;
            alert("修改成功");
            athis.queryData();
          }).catch(function () {
            alert("修改失败");
          })
        },
        imgCallBack:function(response, file, fileList){ //图片上传的回调函数
          // 赋值
          console.log(response);
          this.addShop.imgPath=response.data;

          // console.log("修改之后的回显"+this.updateBrandForm.imgpath)
        }
        ,
        queryPinpais:function(){
          this.$ajax.get("http://localhost:8080/api/brand/queryBrandData").then(res=>{
            this.Pinpais=res.data.data.data;
            console.log(this.Pinpais);

          }).catch(function () {

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
        this.queryPinpais();
      }
    }
</script>

<style scoped>

</style>
