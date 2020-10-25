<template>
  <div class="Page">
    <Category :prop-category="category" @emit-category="changeCategory" />
    <List :prop-datas="currentList" :prop-favorites="favorites" />
  </div>
</template>

<script>
import Vue from "vue";
import VueLazyload from "vue-lazyload";

import Category from "@/components/Category.vue";
import List from "@/components/List.vue";

Vue.use(VueLazyload);

export default {
  name: "Page",
  props: ["propDatas", "propCategory", "propFavorites"],
  components: {
    Category,
    List
  },
  data() {
    return {
      datas: [],
      category: [],
      favorites: [],
      currentList: [],
      emitItem: {}
    };
  },
  methods: {
    changeCategory(target) {
      this.currentList = this.datas.filter(function(item) {
        return item.CAT2 == target;
      });
    }
  },
  mounted() {
    this.currentList = this.datas.slice(0, 9);
  },
  created() {
    let vm = this;

    vm.datas = vm.propDatas;
    vm.category = vm.propCategory;
    vm.favorites = vm.propFavorites;
  },
  beforeDestroy() {
    this.$bus.$emit("page:datas", this.datas);
  }
};
</script>

<style lang="scss">
.Page {
  max-width: 1140px;

  margin: {
    left: auto;
    right: auto;
  }

  @media (max-width: 991px) {
    max-width: 575px;
  }
}
</style>
