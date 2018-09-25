<template>
  <transition name='slide-down'>
      <div class="title-wrapper" v-show="ifTitleMenu">
        <div class="left">
          <span class="iconfont icon">&#xeb94;</span>
        </div>
        <div class="right">
          <div class="icon-wrapper">
            <span class="iconfont icon">&#xe600;</span>
          </div>
          <div class="icon-wrapper">
            <span class="iconfont icon">&#xe684;</span>
          </div>
          <div class="icon-wrapper">
            <span class="iconfont icon">&#xe610;</span>
          </div>
        </div>
      </div>
    </transition>
</template>

<script>
export default {
  name: 'TitleBar',
  props: {
    ifTitleMenu: Boolean
  }
}
</script>

<style lang="scss" scoped>
@import "~@/assets/styles/global";
.title-wrapper {
  position: absolute;
  top: 0;
  left: 0;
  z-index: 101;
  display: flex;
  width: 100%;
  height: px2rem(48);
  background: white;
  box-shadow: 0 px2rem(8) px2rem(8) rgba(0, 0, 0, 0.15);
  .left {
    flex: 0 0 px2rem(60);
    @include center;
  }
  .right {
    flex: 1;
    display: flex;
    justify-content: flex-end;
    .icon-wrapper {
      flex: 0 0 px2rem(40);
      @include center;
    }
  }
}
</style>
