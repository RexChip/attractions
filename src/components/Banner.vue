<template>
  <div class="banner">
    <h1>{{ content }}</h1>
  </div>
</template>

<script>
export default {
  props: ["propContent"],
  data() {
    return {
      content: this.propContent
    };
  }
};
</script>


<style lang="scss">
.banner {
  position: relative;

  display: flex;
  align-items: center;
  justify-content: center;

  height: 50vh;

  // background: linear-gradient(#9198e5, #e66465);
  // background: repeating-linear-gradient(
  //   -45deg,
  //   rgba(#f87f7f, 0.1) 0 20px,
  //   rgba(#0abab5, 0.8) 20px 40px
  // );
  // background-size: 150%;
  // animation: bannerTransform 30s linear infinite;

  background: fixed;
  background-image: url(../image/mark-basarab-1k5Qq4YQM0g-unsplash.jpg);
  background-position: center 300%;
  background-size: cover;

  @keyframes bannerTransform {
    0% {
      background-position: 0;
    }
    100% {
      background-position: 100%;
    }
  }

  @media (max-width: 575px) {
    height: 25vh;
  }

  > h1 {
    position: relative;
    font-family: "Noto Serif TC", serif;
    font-weight: 400;
    text-align: center;

    color: #fff;
    letter-spacing: 0.2em;
    // background-color: #0abab5;
    // text-shadow: -1px -1px #2c3e50;

    padding: {
      top: 5px;
      left: 10px;
      right: 10px;
      bottom: 5px;
    }
  }
}
</style>
