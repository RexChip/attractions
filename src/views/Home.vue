<template>
  <div class="home">
    <Banner :prop-content="'台北旅遊景點'" />

    <div class="home-area">
      <Link />

      <Page
        :prop-datas="originalDatas"
        :prop-category="originalCategory"
        :prop-favorites="originalFavorites"
      />
    </div>
  </div>
</template>

<script>
// @ is an alias to /src
import axios from "axios";
import Banner from "@/components/Banner.vue";
import Link from "@/components/Link.vue";
import Page from "@/components/Page.vue";

export default {
  name: "Home",
  components: {
    Banner,
    Link,
    Page
  },
  data() {
    return {
      originalDatas: [],
      originalCategory: [],
      originalFavorites: []
    };
  },
  methods: {
    localInfo(el) {
      return JSON.parse(localStorage.getItem(el));
    },
    saveInfo(el, info) {
      return localStorage.setItem(el, JSON.stringify(info));
    }
  },
  created() {
    let vm = this;

    if (localStorage.getItem("total-list")) {
      vm.originalDatas = vm.localInfo("total-list");
    }

    if (localStorage.getItem("category-list")) {
      vm.originalCategory = vm.localInfo("category-list");
    }

    if (localStorage.getItem("favorite-list")) {
      vm.originalFavorites = vm.localInfo("favorite-list");
    }
  },
  mounted() {
    let vm = this;

    // 如果沒有暫存的資料，在執行 ajax 取得資料
    if (vm.originalDatas.length != 0) {
      vm.originalDatas = vm.localInfo("total-list");
      vm.originalCategory = vm.localInfo("category-list");
      vm.originalFavorites = vm.localInfo("favorite-list");
    } else {
      const cros = "https://cors-anywhere.herokuapp.com/";
      let api =
        "https://data.taipei/api/v1/dataset/36847f3f-deff-4183-a5bb-800737591de5?scope=resourceAquire";
      axios
        .get(cros + api)
        .then(res => {
          res.data.result.results.forEach(item => {
            vm.originalDatas.push(item);
          });
          vm.originalDatas.forEach(item => {
            let temp = item.CAT2;

            if (vm.originalCategory.indexOf(temp) == -1) {
              vm.originalCategory.push(temp);
            }
          });
        })
        .finally(() => {
          vm.saveInfo("total-list", vm.originalDatas);
          vm.saveInfo("category-list", vm.originalCategory);
          vm.saveInfo("favorite-list", vm.originalFavorites);
        });
    }
  }
};
</script>

<style lang="scss">
.home {
  padding: {
    bottom: 60px;
  }

  &__area {
    max-width: 1170px;

    margin: {
      left: auto;
      right: auto;
    }

    padding: {
      left: 15px;
      right: 15px;
    }
  }
}
</style>
