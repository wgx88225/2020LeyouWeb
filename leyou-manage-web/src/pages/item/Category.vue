<template>
  <v-card>
      <v-flex xs12 sm10>
        <v-tree url="/item/category/list"
                ref="dataRef"
                :isEdit="isEdit"
                @handleAdd="handleAdd"
                @handleEdit="handleEdit"
                @handleDelete="handleDelete"
                @handleClick="handleClick"
        />
      </v-flex>
  </v-card>
</template>

<script>
  export default {
    name: "category",
    data() {
      return {
        isEdit:true,
        category:{},
        recordId:[], // 操作记录Id
      }
    },
    methods: {
      handleAdd(node) {
          // 发起请求
        this.$http({
          url: "/item/category/add",
          method:  "post",
          data: node
        }).then((res) => {
          
         if(res.data == 1){
             this.$message.success("新增成功！");
            //刷新
            this.$refs.dataRef.getData(0);
      
         }else{
             this.$message.error("新增失败！");
         }
         }).catch((e) => {
            console.log(e)
            this.$message.error("新增失败！");

          })
      },
      handleEdit(id, name) {
        console.log(id,name,'update');
       let category = {};
       category.id = id;
       category.name = name;
          // 发起请求
        this.$http({
          url: "/item/category/update",
          method:  "put",
          data: category
        }).then((res) => {
         if(res.data == 1){
             this.$message.success("修改成功！");
             // 刷新
             this.$refs.dataRef.getData(this.category.id);
 
         }else{
             this.$message.error("修改失败！");
         }
          }).catch((e) => {
            console.log(e)
            this.$message.error("修改失败！");

          })
      },
      handleDelete(id) {
           // 发起请求
        this.$http({
          url: "/item/category/delete?id="+id,
          method: "delete",
        }).then((res) => {
                 if(res.data == 1){
             this.$message.success("删除成功！");
               //刷新
              this.$refs.dataRef.getData(0);

         }else{
             this.$message.error("删除失败！");
         }
         }).catch((e) => {
            console.log(e);
             this.$message.error("删除失败！");
          })
        console.log("delete ... " + id)
      },
      // 点击当前节点的方法
      handleClick(node) {
        this.category = node;
        // this.recordId.push(node,id);
        // recordId = Object.assign([],this.recordId);
        // this.recordId = [...new Set(recordId )];
      },
      //刷新页面
      handleRefresh(pid){
           // 发起请求
        this.$http({
          url: "/item/category/list?pid="+pid,
          method:  "get",
        }).then((res) => {
         }).catch((e) => {
            console.log(e)
          })
      }

    }
  };
</script>

<style scoped>

</style>
