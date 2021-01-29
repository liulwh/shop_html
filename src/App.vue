<template>
  <div id="app">

    <router-view/>
  </div>
</template>

<script>
export default {
  name: 'App'
}
</script>

<style>
  @import "./assets/css/main.css";
  @import "./assets/css/color-dark.css";     /*深色主题*/
  /*@import "./assets/css/theme-green/color-green.css";   浅绿色主题*/
  body{
    font-family: "微软雅黑";  /*  设置字体 */
    margin: 0px auto /* 去除上下的边距*/
  }
</style>
