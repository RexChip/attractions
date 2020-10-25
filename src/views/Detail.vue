<template>
  <div class="detail">
    <Banner :prop-content="data.stitle" />

    <div class="detail__item" v-if="active">
      <div class="detail__img" v-lazy:background-image="image[imageIndex]">
        <!-- <img :src="image[imageIndex]" :alt="data.stitle" /> -->

        <div
          class="detail__prev"
          v-if="imageIndex > 0"
          @click="changeImage(-1)"
        ></div>

        <div
          class="detail__next"
          v-if="imageIndex < image.length - 1"
          @click="changeImage(1)"
        ></div>
      </div>

      <div class="detail__wrap">
        <div class="detail__title">
          <h1>{{ data.stitle }}</h1>
          <span class="detail__tag">{{ data.CAT2 }}</span>
        </div>

        <div class="detail__content">{{ data.xbody }}</div>

        <div class="detail__time" v-if="data.MEMO_TIME">
          <h3>開放時間：</h3>
          <p>{{ data.MEMO_TIME }}</p>
        </div>

        <div class="detail__info" v-if="data.info">
          <h3>交通資訊：</h3>
          <p>{{ data.info }}</p>
        </div>

        <div class="detail__mrt" v-if="data.MRT">
          <h3>捷運站：</h3>
          <p>{{ data.MRT }}</p>
        </div>
      </div>
    </div>

    <router-link :to="'/'" class="detail__back">Back</router-link>
  </div>
</template>

<script>
import Vue from "vue";
import axios from "axios";
import VueLazyload from "vue-lazyload";
import Banner from "@/components/Banner.vue";

Vue.use(VueLazyload);

export default {
  components: {
    Banner
  },
  data() {
    return {
      data: {},
      image: [],
      imageIndex: 0,
      active: false
    };
  },
  methods: {
    changeImage(target) {
      this.imageIndex += target;
    },
    localInfo(el) {
      return JSON.parse(localStorage.getItem(el));
    }
  },
  mounted() {
    let vm = this;
    let api =
      "https://data.taipei/api/v1/dataset/36847f3f-deff-4183-a5bb-800737591de5?scope=resourceAquire";

    // 直接輸入網址的話，就用 ajax 取得資料
    if (vm.data.stitle == undefined) {
      axios
        .get(api)
        .then(res => {
          console.log("aixos");
          let datas = res.data.result.results;
          let temp = datas.filter(function(item) {
            return item["_id"] == vm.$route.params.id;
          });

          vm.data = temp[0];
          // vm.image = "http://" + temp[0].file.split("http://")[1];

          let tempImage = vm.data.file.split("http://").slice(1);
          tempImage.forEach(item => {
            if (item.indexOf(".mp3") == -1) {
              vm.image.push("http://" + item);
            }
          });
        })
        .finally(() => {
          vm.active = true;
        });
    }
  },
  created() {
    let vm = this;

    this.$bus.$on("page:item", item => {
      let tempImage = [];

      vm.data = item;

      tempImage = vm.data.file.split("http://").slice(1);
      tempImage.forEach(item => {
        if (item.indexOf(".mp3") == -1) {
          vm.image.push("http://" + item);
        }
      });

      vm.active = true;
    });

    if (vm.localInfo("current-item") !== null) {
      let tempImage = [];

      vm.data = vm.localInfo("current-item");

      tempImage = vm.data.file.split("http://").slice(1);
      tempImage.forEach(item => {
        if (item.indexOf(".mp3") == -1) {
          vm.image.push("http://" + item);
        }
      });

      vm.active = true;
    }
  },
  beforeDestroy() {
    this.$bus.$off("page:item");
  }
};
</script>

<style lang="scss">
@import "@/scss/_var.scss";

.detail {
  position: relative;

  background-color: rgba($tiffany, 0.05);

  padding: {
    bottom: 90px;
  }

  &__item {
    position: relative;
    max-width: 960px;

    box-shadow: 0 0 20px rgba(#000, 0.15);
    background-color: #fff;
    border-radius: 5px;

    z-index: 1;

    margin: {
      top: -15vh;
      left: auto;
      right: auto;
    }

    padding: {
      top: 60px;
      left: 60px;
      right: 60px;
      bottom: 60px;
    }
  }

  &__img {
    position: relative;

    width: 600px;
    max-width: 100%;

    box-shadow: 0 2px 10px rgba(#000, 0.2);
    background-position: center;
    background-repeat: no-repeat;
    background-size: cover;

    margin: {
      left: auto;
      right: auto;
    }

    &::before {
      content: "";
      display: block;
      width: 100%;
      padding-top: 50%;
    }

    &[lazy="loading"] {
      &:after {
        content: "";
        position: absolute;
        top: 50%;
        left: 50%;

        width: 60px;
        height: 60px;

        background: linear-gradient(rgba($tiffany, 0.3), $tiffany);
        animation: loading 2s linear infinite;

        border-radius: 50%;

        margin: {
          top: -30px;
          left: -30px;
        }
      }
    }

    img {
      position: absolute;
      top: 0;
      left: 0;

      display: block;
      width: 100%;
      height: 100%;
      object-fit: cover;
      object-position: center;
    }
  }

  &__prev,
  &__next {
    position: absolute;
    top: 50%;

    display: flex;
    align-items: center;
    justify-content: center;

    width: 40px;
    height: 40px;

    background-color: $tiffany;
    border-radius: 50%;

    cursor: pointer;

    margin: {
      top: -20px;
    }

    &::before {
      content: "";
      display: block;
    }
  }

  &__prev {
    @media (min-width: 768px) {
      right: calc(100% + 10px);
    }

    @media (max-width: 767px) {
      left: 10px;
    }

    &::before {
      display: block;
      width: 0;
      height: 0;
      border-style: solid;
      border-width: 8px 10px 8px 0;
      border-color: transparent #fff transparent transparent;
    }
  }

  &__next {
    @media (min-width: 768px) {
      left: calc(100% + 10px);
    }

    @media (max-width: 767px) {
      right: 10px;
    }

    &::before {
      width: 0;
      height: 0;
      border-style: solid;
      border-width: 8px 0 8px 10px;
      border-color: transparent transparent transparent #fff;
    }
  }

  &__wrap {
    padding: {
      top: 30px;
    }

    > div:not(:first-child) {
      padding: {
        top: 30px;
      }
    }
  }

  &__title {
    display: flex;
    align-items: center;
    flex-wrap: wrap;
    gap: 10px;

    h1 {
      font-family: "Noto Serif TC", serif;
      font-weight: 400;
      font-size: 22px;
    }
  }

  &__tag {
    flex-shrink: 0;

    display: inline-block;

    font-size: 14px;
    color: #fff;

    background-color: $tiffany;

    padding: {
      top: 5px;
      left: 10px;
      right: 10px;
      bottom: 5px;
    }
  }

  &__content,
  &__time,
  &__info {
    line-height: 1.7;
  }

  h3 {
    font-family: "Noto Serif TC", serif;
    font-size: 20px;
    font-weight: 400;
  }

  &__back {
    display: block;
    width: 80px;

    background-color: $tiffany;
    color: #fff;

    text-decoration: none;
    transition: all 0.3s;
    border-radius: 3px;

    margin: {
      top: 30px;
      left: auto;
      right: auto;
    }

    padding: {
      top: 5px;
      left: 20px;
      right: 20px;
      bottom: 5px;
    }

    &:hover {
      opacity: 0.7;
    }
  }
}
</style>