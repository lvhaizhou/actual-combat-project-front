<template>
  <div>
    <!-- <div>导航</div> -->
    <nuxt />
    <!-- <div>底部信息</div> -->
  </div>
</template>

<script>
export default {
}
</script>

<style lang="scss">
.login-container{
  width: 100%;
  min-height: 100%;
  .login-form{
    width: 520px;
    padding: 160px 0;
    margin: 0 auto;
    overflow: hidden;
  }
  .title-container{
    text-align: center;
    img{
      width: 200px;
      margin-bottom: 20px;
    }
  }
  .captcha-container{
    width: 340px;
    position: relative;
    .captcha{
      position: absolute;
      right: -110px;
      img{
        width: 90px;
        height: 50px;
        cursor: pointer;
      }
    }
    .el-button{
      width: 90px;
      padding: 0;
      line-height: 40px;
    }
  }
}
</style>
