<template>
<div class='menu-bar'>
  <transition name='slide-up' >
    <div class="menu-wrapper" v-show="ifTitleMenu"
    :class="{'hide-box-shadow' : isSetting ||!ifTitleMenu}">
      <div class="icon-wrapper">
        <span class="iconfont icon" @click="showSetting(3)">&#xe614;</span>
      </div>
      <div class="icon-wrapper">
        <span class="iconfont icon progress" @click="showSetting(2)">&#xe62c;</span>
      </div>
      <div class="icon-wrapper">
        <span class="iconfont icon" @click="showSetting(1)">&#xe625;</span>
      </div>
      <div class="icon-wrapper">
        <span class=" icon" @click='showSetting(0)'>A</span>
      </div>
    </div>
  </transition>
  <transition name='slide-up'>
    <div class="setting" v-show='isSetting'>
      <div class="setting-font" v-if="showTag===0">
        <div class="show" :style="{fontSize:fontSizeList[0].fontSize+'px'}">A</div>
        <div class="select-wrapper">
          <div class="selected" v-for="(item,index) in fontSizeList" :key="index"
          @click="setFontSize(item.fontSize)">
            <div class="line"></div>
            <div class="point-wrapper" >
              <div class="point" v-show="defaultFontSize===item.fontSize" >
                <div class="small-point"></div>
              </div>
            </div>
            <div class="line"></div>
          </div>
        </div>
        <div class="show" :style="{fontSize:fontSizeList[fontSizeList.length-1].fontSize+'px'}">A</div>
      </div>
      <div class="setting-theme" v-else-if="showTag===1">
        <div class="setting-item" v-for="(item,index) in themeList" :key='index' @click="setTheme(index)">
          <div class="preview" :style="{background:item.style.body.background}"
          :class="{'no-border':item.style.body.background !=='#fff'}"></div>
          <div class="text" :class="{'selected':index===defaultTheme}">{{item.name}}</div>
        </div>
      </div>
      <div class="setting-progress" v-else-if="showTag===2">
        <div class="progress-wrapper">
          <input class="progress"
          type="range"
          max="100"
          min="0"
          step="1"
          @change="onProgressChange($event.target.value)"
          @input="onProgressInput($event.target.value )"
          :value="progress"
          :disabled="!bookAvailable"
          ref="progress"/>
          <div class="text-wrapper">
            <span>{{bookAvailable?progress+'%':'加载中...'}}</span>
          </div>
        </div>
      </div>
    </div>
  </transition>
  <content-view :ifShowContent="ifShowContent"
  v-show="ifShowContent"
  :navigation="navigation"
  :bookAvailable="bookAvailable"
  @jumpTo="jumpTo"></content-view>
    <transition name="fade">
      <div class="content-mask" v-show="ifShowContent" @click="hideContent">
      </div>
    </transition>
</div>
</template>

<script>
import ContentView from '@/components/ContentView'
export default {
  name: 'MenuBar',
  components: {
    ContentView
  },
  props: {
    ifTitleMenu: Boolean,
    fontSizeList: Array,
    defaultFontSize: Number,
    themeList: Array,
    defaultTheme: Number,
    bookAvailable: Boolean,
    navigation: {}
  },
  data() {
    return {
      isSetting: false,
      showTag: 0,
      progress: 0,
      ifShowContent: false
    }
  },
  methods: {
    hideContent() {
      this.ifShowContent = false
    },
    setFontSize(fontSize) {
      this.$emit('setFontSize', fontSize)
    },
    showSetting(tag) {
      this.showTag = tag
      if (this.showTag === 3) {
        this.isSetting = false
        this.ifShowContent = true
      } else {
        this.isSetting = true
      }
    },
    hiddenSetting() {
      this.isSetting = false
    },
    setTheme(index) {
      this.$emit('setTheme', index)
    },
    onProgressInput(progress) {
      this.progress = progress
      this.$refs.progress.style.backgroundSize = `${this.progress}% 100%`
    },
    onProgressChange(progress) {
      this.$emit('onProgressChange', progress)
    },
    jumpTo(target) {
      this.$emit('jumpTo', target)
    }
  }
}
</script>

<style lang="scss" scoped>
@import "~@/assets/styles/global";
.menu-bar {
  .menu-wrapper {
    position: absolute;
    bottom: 0;
    left: 0;
    z-index: 101;
    display: flex;
    width: 100%;
    height: px2rem(48);
    background: white;
    box-shadow: 0 px2rem(-8) px2rem(8) rgba(0, 0, 0, 0.15);
    .icon-wrapper {
      flex: 1;
      @include center;
      .progress {
        font-size: 30px;
      }
    }
    &.hide-box-shadow {
      box-shadow: none;
    }
  }
  .setting {
    position: absolute;
    bottom: px2rem(48);
    left: 0;
    width: 100%;
    height: px2rem(60);
    background: white;
    box-shadow: 0 px2rem(-8) px2rem(8) rgba(0, 0, 0, 0.15);
    .setting-font {
      display: flex;
      height: 100%;
      .show {
        flex: 0 0 px2rem(40);
        @include center;
      }
      .select-wrapper {
        display: flex;
        flex: 1;
        .selected {
          flex: 1;
          display: flex;
          align-items: center;
          &:first-child {
            .line {
              &:first-child {
                border: none;
              }
            }
          }
          &:last-child {
            .line {
              &:last-child {
                border: none;
              }
            }
          }
          .line {
            flex: 1;
            height: 0;
            border-top: px2rem(1) solid #ccc;
          }
          .point-wrapper {
            position: relative;
            flex: 0 0 0;
            width: 0;
            height: px2rem(7);
            border-left: px2rem(1) solid #ccc;
            .point {
              position: absolute;
              top: px2rem(-6);
              left: px2rem(-10);
              width: px2rem(16);
              height: px2rem(16);
              border-radius: 50%;
              border: px2rem(1) solid #ccc;
              background: white;
              box-shadow: 0 px2rem(4) px2rem(4) rgba(0, 0, 0, 0.15);
              @include center;
              .small-point {
                width: px2rem(5);
                height: px2rem(5);
                background: black;
                border-radius: 50%;
              }
            }
          }
        }
      }
    }
    .setting-theme {
      display: flex;
      height: 100%;
      .setting-item {
        flex: 1;
        display: flex;
        flex-direction: column;
        box-sizing: border-box;
        padding: px2rem(5);
        .preview {
          flex: 1;
          border: px2rem(1) solid #ccc;
          &.no-border {
            border: none;
          }
        }
        .text {
          flex: 0 0 px2rem(20);
          font-size: px2rem(16);
          color: #999;
          @include center;
          &.selected {
            color: #333;
          }
        }
      }
    }
    .setting-progress {
      position: relative;
      width: 100%;
      height: 100%;
      .progress-wrapper {
        width: 100%;
        height: 100%;
        @include center;
        padding: 0 px2rem(30);
        box-sizing: border-box;
        display: flex;
        flex-direction: column;
        .progress {
          width: 100%;
          -webkit-appearance: none;
          height: px2rem(2);
          background: -webkit-linear-gradient(#999, #999) no-repeat, #ddd;
          background-size: 0 100%;
          &:focus {
            outline: none;
          }
          &::-webkit-slider-thumb {
            -webkit-appearance: none;
            height: px2rem(20);
            width: px2rem(20);
            border-radius: 50%;
            background: white;
            box-shadow: 0 4px 4px 0 rgba(219, 19, 19, 0.15);
            border: px2rem(1) solid #ddd;
          }
        }
        .text-wrapper {
          flex: 0 0 px2rem(20);
          font-size: px2rem(16);
          color: #999;
          @include center;
          padding-top: px2rem(10);
        }
      }
    }
  }
  .content-mask {
    position: absolute;
    top: 0;
    left: 0;
    z-index: 101;
    display: flex;
    width: 100%;
    height: 100%;
    background: rgba(51, 51, 51, 0.8);
  }
}
</style>
