<template>
  <v-form ref="brandForm" v-model="valid">
    <v-text-field
      v-model="brand.name"
      label="品牌名称"
      required
      :rules="[
        v => !!v || '品牌名称不能为空！',
        v => v.length <= 10 || '品牌名称长度不超过10位！',
        ]"
    ></v-text-field>
    <v-text-field
      v-model="brand.letter"
      label="品牌首字母"
      required
      mask="A"
      :rules="[
        v => !!v || '品牌首字母不能为空！',
        ]"
    ></v-text-field>
    <v-cascader
      url="/item/category/list"
      multiple
      required
      v-model="brand.categories"
      label="请选择商品分类"
      :rules="[
        v => !!v || '商品分类不能为空！'

        ]"
    />
    <v-layout row>
      <v-flex xm4 class="subheader">请上传品牌LOGO:</v-flex>
      <v-flex>
        <v-upload
          v-model="brand.image"
          url="/item/upload"
          :multiple="false"
          :pic-width="250"
          :pic-height="90"
        />
      </v-flex>
    </v-layout>
    <v-layout row class="pt-5">
      <v-spacer />
      <v-btn small class="primary" @click="submit">提交</v-btn>
      <v-btn small class="warning" @click="resetValidation">重置</v-btn>
      <v-btn small>取消</v-btn>
    </v-layout>
  </v-form>
</template>

<script>
export default {
  name: "my-brand-form",

  data() {
    return {
      valid: false,
      brand: {
        name: "",
        letter: "",
        image: "",
        categories: []
      }
    };
  },

  methods: {
    submit() {
      if (this.$refs.brandForm.validate()) {
        // 解构表达式获取品牌中的数据，除了categories以外的存储到rest对象中
        const { categories, ...rest } = this.brand;
        // 对categories进行处理，得到里面的id，赋值给rest中的categories
        rest.categories = categories.map(c => c.id);
        // rest.categories = rest.categories +"";
        rest.categories = rest.categories.join(",");
        this.$http
          .post("/item/brand/add", this.$qs.stringify(rest))
          .then(resp => {
            
            if(resp.data == 1){
               this.$message.success("新增品牌成功！");
            }else if(resp.data == 0){
               this.$message.error("品牌名已被占用！");
            }else{
               this.$message.error("新增品牌失败\！");
            }
           this.$emit('addclose', false) // 用来触发父组件定义
          })
          .catch(() => {
            this.$message.error("新增品牌失败，出现异常！");
              this.$emit('addclose', false) // 用来触发父组件定义
          });
      }
    },
    resetValidation() {
      this.$refs.brandForm.resetValidation();
    }
  },
  created() {},
  mounted() {},
  //  监听数据变化
  watch: {}
};
</script>
<style scoped>
</style>