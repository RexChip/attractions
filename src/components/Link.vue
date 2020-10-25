<template>
  <div id="nav">
    <router-link to="/">HOME</router-link>
    <router-link to="/favorite">FAVORITE</router-link>
  </div>
</template>

<style lang="scss">
#nav {
  display: flex;
  align-items: center;
  justify-content: center;
  flex-wrap: wrap;
  gap: 20px;

  padding: 30px 15px;

  a {
    font-weight: bold;
    text-decoration: none;
    color: #2c3e50;

    &.router-link-exact-active {
      color: #0abab5;
    }
  }
}
</style>