<template>
  <v-card>
    <v-card-title class="layout row">
      <v-btn small color="primary" @click="show=true">新增品牌</v-btn>
      <v-spacer />
      <v-text-field
        class="flex sm3"
        label="输入搜索条件"
        append-icon="search"
        hide-details
        single-line
        v-model="search.key"
      />
    </v-card-title>
    <v-divider />
    <v-data-table
      :headers="headers"
      :items="brands"
      :pagination.sync="pagination"
      :total-items="totalBrands"
      :loading="loading"
      class="elevation-1"
    >
      <template slot="items" slot-scope="props">
        <td class="text-xs-center">{{props.item.id}}</td>
        <td class="text-xs-center">{{props.item.name}}</td>
        <td class="text-xs-center">
          <img :src="props.item.image" />
        </td>
        <td class="text-xs-center">{{props.item.letter}}</td>
        <td class="justify-center layout px-1">
          <!-- <v-btn small icon class="mx-0" @click="editItem(props.item)">
            <v-icon color="info">edit</v-icon>
          </v-btn>
          <v-btn small icon class="mx-0" @click="deleteItem(props.item)">
            <v-icon color="warning">delete</v-icon>
          </v-btn>-->
          <v-btn small color="info" @click="editItem(props.item)">编辑</v-btn>
          <v-btn small color="warning" @click="deleteItem(props.item)">删除</v-btn>
        </td>
      </template>
    </v-data-table>
    <v-dialog v-model="show" max-width="500" scrollable persistent>
      <v-card>
        <!-- <v-card-title class="elevation-2">新增品牌</v-card-title> -->
        <v-toolbar dense color="primary" dark class="title px-2"><span>新增品牌</span>
        <v-spacer />
        <v-btn icon @click="show = false">
          <v-icon>close</v-icon>
          
        </v-btn>

        </v-toolbar>
        <v-divider></v-divider>
        <v-card-text style="height:600px" class="px-5">
          <my-brand-form @addclose="addclose"/>
        </v-card-text>
      </v-card>
    </v-dialog>
  </v-card>
</template>

<script>
 import MyBrandForm from './MyBrandForm'
export default {
  components:{
MyBrandForm
  },
  data() {
    return {
      headers: [
        {
          text: "ID",
          value: "id",
          align: "center",
          sortable: true,
          width: "50px"
        },
        { text: "名称", value: "name", align: "center", sortable: false },
        { text: "LOGO", value: "image", align: "center", sortable: false },
        { text: "首字母", value: "letter", align: "center", sortable: false },
        { text: "操作", align: "center", value: "id", sortable: false }
      ],
      brands: [],
      loading: false,
      totalBrands: 0,
      pagination: {},
      search: { key: "" },
      param: {},
      show: false
    };
  },
  //  监听数据变化
  methods: {
    addclose(val){
     this.show = val;
    },
    /**
     * 从服务器获取数据
     */
    getDataFromServcer() {
      this.param = Object.assign(this.search, this.pagination);
  
      this.loading = true;
      // 发起请求
      this.$http
        .post("/item/brand/page", this.param)
        .then(res => {
          if (res.status == 200) {
            this.brands = res.data.items;
            this.totalBrands = res.data.total;
          } else {
            this.brands = [];
            this.totalBrands = 0;
          }

          this.loading = false;
        })
        .catch(e => {
          console.log(e);
          this.$message.error("查询出现异常！");
          this.loading = false;
        });
    },

    /** 搜索方法 */

    /** 新增方法 */
    addItem() {},
    /** 编辑方法 */
    editItem(obj) {
      console.log(obj, "update");
    },
    /** 删除方法 */
    deleteItem(obj) {
      console.log(obj, "delete");
    }
  },
  created() {
    this.getDataFromServcer();
  },
  mounted() {},
  watch: {
    pagination: {
      deep: true,
      handler() {
        this.getDataFromServcer();
      }
    },
    "search.key"(newVal, oldVal) {
      this.getDataFromServcer();
    }
  }
};
</script>
<style scoped>
</style>