<template>
  <div class="favorite">
    <Banner :prop-content="'最愛景點資訊'" />
    <Link />

    <div class="favorite__area">
      <List :prop-datas="computeFavorite" :prop-favorites="favorites" />
    </div>
  </div>
</template>

<script>
import Banner from "@/components/Banner.vue";
import Link from "@/components/Link.vue";
import List from "@/components/List.vue";

export default {
  data() {
    return {
      datas: [],
      category: [],
      favorites: []
    };
  },
  components: {
    Banner,
    Link,
    List
  },
  computed: {
    computeFavorite() {
      let vm = this;

      return vm.datas.filter(item => {
        return vm.favorites.indexOf(item["_id"]) != -1;
      });
    }
  },
  methods: {
    favoriteStatus(item) {
      return this.favorites.indexOf(item["_id"]) != -1 ? "active" : "";
    },
    toggleFavorite(item) {
      let vm = this;

      if (vm.favorites.indexOf(item["_id"] == -1)) {
        vm.favorites.push(item["_id"]);
      } else {
        vm.favorites.splice(vm.favorites.indexOf(item["_id"]), 1);
      }

      vm.saveInfo("favorite-list", vm.favorites);
    },
    localInfo(el) {
      return JSON.parse(localStorage.getItem(el));
    },
    saveInfo(el, info) {
      return localStorage.setItem(el, JSON.stringify(info));
    }
  },
  created() {
    let vm = this;

    vm.$bus.$on("page:datas", item => {
      vm.datas = item;
    });

    vm.$bus.$on("page:category", item => {
      vm.category = item;
    });

    vm.$bus.$on("page:favorites", item => {
      vm.favorites = item;
    });
  },
  mounted() {
    let vm = this;

    if (vm.datas.length == 0) {
      vm.datas = vm.localInfo("total-list");
    }

    if (vm.favorites.length == 0) {
      vm.favorites = vm.localInfo("favorite-list");
    }
  },
  beforeDestroy() {
    // this.$bus.$off("page:favorites");

    this.$bus.$off("page:datas");
    this.$bus.$off("page:category");
    this.$bus.$off("page:favorites");
  }
};
</script>

<style lang="scss">
$tiffany: #0abab5;
$gap: 30px; // 列表間距
$num: 3; // 每行的數量

.favorite {
  &__area {
    max-width: 1140px;

    margin: {
      left: auto;
      right: auto;
    }
    padding: {
      bottom: 60px;
    }

    @media (max-width: 991px) {
      max-width: 575px;
    }
  }
}
</style>