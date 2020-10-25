<template>
  <div class="list">
    <div v-for="(item, index) in datas" :key="index" class="list__item">
      <div
        class="list__favorite"
        :class="favoriteStatus(item)"
        @click="toggleFavorite(item, index)"
      ></div>

      <div
        class="list__img"
        v-lazy:background-image="'http://' + item.file.split('http://')[1]"
      ></div>

      <div class="list__wrap">
        <div class="list__title">
          <span>{{ item.stitle }}</span>
          <span class="list__tag">{{ item.CAT2 }}</span>
        </div>
        <div class="list__content">{{ item.xbody }}</div>
      </div>

      <!-- 使用 event bus 的話不能使用 router link，必須使用 method 推到 router 上 -->
      <!-- <router-link
          :to="{
            path: '/detail/' + item['_id'],
            query: { id: item['_id'], item: item }
          }"
        ></router-link> -->
      <a href="#" @click.prevent="emitData(item)"></a>
    </div>
  </div>
</template>

<script>
export default {
  props: ["propDatas", "propFavorites"],
  data() {
    return {
      // datas: this.propDatas,
      // favorites: this.propFavorites,
      emitItem: {}
    };
  },
  methods: {
    emitData(item) {
      this.emitItem = item;
      this.saveInfo("current-item", item);
      this.$router.push({ path: "/detail/" + item["_id"] });
    },
    favoriteStatus(item) {
      return this.favorites.indexOf(item["_id"]) != -1 ? "active" : "";
    },
    toggleFavorite(item) {
      let vm = this;

      if (vm.favorites.indexOf(item["_id"]) == -1) {
        vm.favorites.push(item["_id"]);
      } else {
        vm.favorites.splice(vm.favorites.indexOf(item["_id"]), 1);
      }

      vm.saveInfo("favorite-list", vm.favorites);

      // 改變狀態
      // let tempStatus = !item.favoriteStatus;
      // vm.datas[index].favoriteStatus = tempStatus;

      // if (tempStatus == true) {
      //   // 如果列表裡面沒有這筆資料就加到最愛列表
      //   let tempItem = vm.favorites.find(target => {
      //     return target["_id"] == item["_id"];
      //   });

      //   if (tempItem == undefined) {
      //     vm.favorites.push(item);
      //     vm.saveInfo("favorite-list", vm.favorites);
      //   }
      // } else {
      //   // 移除最愛
      //   vm.favorites.find((target, index) => {
      //     if (target["_id"] == item["_id"]) {
      //       vm.favorites.splice(index, 1)
      //     }
      //   });

      //   vm.saveInfo("favorite-list", vm.favorites);
      // }
    },
    saveInfo(el, info) {
      return localStorage.setItem(el, JSON.stringify(info));
    }
  },
  computed: {
    datas() {
      return this.propDatas;
    },
    favorites() {
      return this.propFavorites;
    }
  },
  beforeDestroy() {
    // 使用 router 必須在 beforeDestroy 執行 event bus，不然會因為執行時間的問題，導致無法傳送資料
    this.$bus.$emit("page:item", this.emitItem);
    this.$bus.$emit("page:category", this.category);
    this.$bus.$emit("page:favorites", this.favorites);
  }
};
</script>

<style lang="scss">
@import "@/scss/_var.scss";

.list {
  display: flex;
  flex-wrap: wrap;
  gap: $gap;

  padding: {
    top: 60px;
  }

  @media (max-width: 991px) {
    gap: 20px;

    padding: {
      top: 30px;
    }
  }

  &__item {
    position: relative;

    display: flex;
    flex-direction: column;
    justify-content: flex-start;

    width: calc((100% - (#{$gap} * (#{$num} - 1))) / #{$num});

    box-shadow: 0 0 5px rgba(#2c3e50, 0.3);
    border-radius: 5px;
    overflow: hidden;

    transition: all 0.3s;

    @media (max-width: 991px) and (min-width: 576px) {
      width: calc((100% - 20px) / 2);
    }

    @media (max-width: 575px) {
      width: 100%;
    }

    &:hover {
      box-shadow: 0 0 20px rgba(#aaa, 0.5);
      transform: translateY(-5px);

      &__title {
        span:first-child {
          color: $tiffany;
        }
      }
    }

    > a {
      position: absolute;
      top: 0;
      left: 0;
      width: 100%;
      height: 100%;
    }
  }

  &__favorite {
    position: absolute;
    top: 10px;
    right: 10px;

    width: 20px;
    height: 20px;

    border-radius: 50%;
    border: solid 3px #fff;
    background-color: #fff;
    box-shadow: 0 0 10px rgba(#000, 0.15);

    z-index: 1;

    cursor: pointer;

    &:hover {
      &::after {
        right: calc(100% + 10px);
        opacity: 1;
      }
    }

    &::before {
      content: "";
      position: absolute;
      top: 50%;
      left: 50%;

      width: 40px;
      height: 40px;

      border-radius: 50%;

      margin: {
        top: -20px;
        left: -20px;
      }
    }

    &::after {
      content: "加到最愛";

      position: absolute;
      top: 50%;
      right: 100%;

      display: block;
      width: 80px;

      font-size: 14px;
      font-weight: 300;
      line-height: 30px;
      color: #fff;

      background-color: #f87f7f;
      border-radius: 5px;

      pointer-events: none;
      text-align: center;
      white-space: pre;

      transition: all 0.3s ease;
      opacity: 0;

      margin: {
        top: -15px;
      }
    }

    &.active {
      background-color: #f87f7f;

      &::after {
        content: "移除最愛";
      }
    }
  }

  &__img {
    position: relative;
    width: 100%;
    // height: 250px;

    background-color: #eee;
    background-repeat: no-repeat;
    background-position: center;
    background-size: cover;

    &:before {
      content: "";
      display: block;
      width: 100%;
      padding-top: 75%;
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
  }

  &__wrap {
    padding: {
      top: 20px;
      left: 20px;
      right: 20px;
      bottom: 20px;
    }
  }

  &__title {
    display: flex;
    align-items: center;
    justify-content: space-between;

    font-family: "Noto Serif TC", serif;
    font-size: 22px;

    @media (max-width: 991px) {
      font-size: 18px;
    }

    span:first-child {
      transition: color 0.3s;

      text-overflow: ellipsis;
      white-space: nowrap;
      overflow: hidden;

      flex-grow: 1;
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

  &__content {
    // 多行 ...
    display: -webkit-box;
    -webkit-box-orient: vertical;
    -webkit-line-clamp: 3;
    text-overflow: ellipsis;
    overflow: hidden;

    height: 66px;
    font-size: 14px;
    line-height: 1.6;
    letter-spacing: 0.05em;

    margin: {
      top: 10px;
    }
  }
}

@keyframes loading {
  0% {
    transform: rotate(0);
  }
  100% {
    transform: rotate(360deg);
  }
}
</style>