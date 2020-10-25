<template>
  <div class="category">
    <div
      class="category__item"
      v-for="(item, index) in category"
      :key="index"
      @click="emitData(item)"
    >
      {{ item }}
    </div>
  </div>
</template>

<script>
export default {
  name: "Category",
  props: ["propCategory"],
  data() {
    return {
      category: this.propCategory
    };
  },
  methods: {
    emitData(item) {
      this.$emit("emit-category", item);
    }
  }
};
</script>

<style lang="scss">
@import "@/scss/_var.scss";

.category {
  display: flex;
  flex-wrap: wrap;
  gap: 10px;

  &__item {
    font-size: 16px;
    font-weight: 300;
    color: #fff;

    border-radius: 5px;
    background-color: rgba($tiffany, 0.8);
    cursor: pointer;

    transition: all 0.3s;

    padding: {
      top: 5px;
      left: 10px;
      right: 10px;
      bottom: 5px;
    }

    &:hover {
      box-shadow: 0 2px 10px rgba(#2c3e50, 0.2);
      transform: translateY(-3px);
    }

    @media (max-width: 575px) {
      flex: 0 1 calc((100% - 20px) / 3);
      text-align: center;
    }
  }
}
</style>