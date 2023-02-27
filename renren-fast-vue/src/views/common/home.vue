<template>
  <div class="mod-home">
    <h3 style="text-align: center;color: #3E8EF7;font-size: 50px"> {{ username }}, 欢迎使用管理系统</h3>
  </div>
</template>

<script>
  export default {
    data () {
      return {
        username: sessionStorage.getItem('username')
      }
    }
  }
</script>

<style>
  .mod-home {
    line-height: 1.5;
  }
</style>

