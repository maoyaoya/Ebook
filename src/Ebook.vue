<template>
  <div class='ebook'>
    <title-bar :ifTitleMenu="ifTitleMenu"></title-bar>
    <div class='read-wrapper'>
        <div id='read'></div>
      <!-- 实现电子书的翻页功能 -->
      <div class="mask">
        <div class="left" @click='prevPage'></div>
        <div class="center" @click="toggle"></div>
        <div class="right" @click='nextPage'></div>
      </div>
    </div>
    <menu-bar :ifTitleMenu="ifTitleMenu"
    :fontSizeList="fontSizeList"
    :defaultFontSize="defaultFontSize"
    :themeList="themeList"
    :defaultTheme="defaultTheme"
    :bookAvailable="bookAvailable"
    @onProgressChange="onProgressChange"
    @setTheme="setTheme"
    @setFontSize="setFontSize"
    @jumpTo="jumpTo"
    :navigation="navigation"
    ref="menu"></menu-bar>
  </div>
</template>

<script>
import MenuBar from './components/MenuBar'
import TitleBar from './components/TitleBar'
import Epub from 'epubjs'
const DOWNLOAD_URL = '/static/2018_Book_AgileProcessesInSoftwareEngine.epub'
global.epub = Epub
/* eslint-disable space-before-function-paren */
export default {
  components: {
    TitleBar,
    MenuBar
  },
  data() {
    return {
      ifTitleMenu: false,
      fontSizeList: [
        { fontSize: 12 },
        { fontSize: 14 },
        { fontSize: 16 },
        { fontSize: 18 },
        { fontSize: 20 },
        { fontSize: 22 },
        { fontSize: 24 }
      ],
      defaultFontSize: 16,
      themeList: [
        {
          name: 'default',
          style: {
            body: {
              'color': '#000',
              'background': '#fff'
            }
          }
        },
        {
          name: 'eye',
          style: {
            body: {
              'color': '#000',
              'background': '#ceeaba'
            }
          }
        },
        {
          name: 'night',
          style: {
            body: {
              'color': '#fff',
              'background': '#000'
            }
          }
        }, {
          name: 'gold',
          style: {
            body: {
              'color': '#000',
              'background': 'rgb(241,236,226)'
            }
          }
        }
      ],
      defaultTheme: 0,
      bookAvailable: false,
      navigation: {}
    }
  },
  methods: {
    // 电子书的解析和渲染
    showEpub() {
      // 生成book
      this.book = new Epub(DOWNLOAD_URL)
      // 生成Rendition
      this.rendition = this.book.renderTo('read', {
        width: window.innerWidth,
        height: window.innerHeight
      })
      // 通过Rendtion.display渲染电子书
      this.rendition.display()
      // 获取Theme对象
      this.themes = this.rendition.themes
      // 设置默认字体
      this.setFontSize(this.defaultFontSize)
      // 将主题注册到themes对象中register(名称，样式)
      // themes提供select方法切换主题
      this.registerTheme()
      // 设置默认样式
      this.setTheme(this.defaultTheme)
      // 获取location对象
      // 通过epubjs的钩子函数来实现
      this.book.ready.then(() => {
        this.navigation = this.book.navigation
        return this.book.locations.generate()
      }).then(res => {
        this.locations = this.book.locations
        this.bookAvailable = true
      })
    },
    // 根据链接跳转到指定位置
    jumpTo(href) {
      this.rendition.display(href)
      this.hideTitleAndMenu()
    },
    hideTitleAndMenu() {
      // 隐藏标题栏和菜单栏
      this.ifTitleMenu = false
      // 隐藏菜单栏弹出的设置栏
      this.$refs.menu.hiddenSetting()
      // 隐藏目录
      this.$refs.menu.hideContent()
    },
    // progress进度条的数值 0-100
    onProgressChange(progress) {
      const percentage = progress / 100
      const location = percentage > 0 ? this.locations.cfiFromPercentage(percentage) : 0
      this.rendition.display(location)
    },
    setTheme(index) {
      this.themes.select(this.themeList[index].name)
      this.defaultTheme = index
    },
    registerTheme() {
      this.themeList.forEach(theme => {
        this.themes.register(theme.name, theme.style)
      })
    },
    setFontSize(fontSize) {
      this.defaultFontSize = fontSize
      if (this.themes) {
        this.themes.fontSize(fontSize + 'px')
      }
    },
    prevPage() {
      if (this.rendition) {
        this.rendition.prev()
      }
    },
    nextPage() {
      if (this.rendition) {
        this.rendition.next()
      }
    },
    toggle() {
      this.ifTitleMenu = !this.ifTitleMenu
      if (!this.ifTitleMenu) {
        this.$refs.menu.hiddenSetting()
      }
    }
  },
  mounted() {
    this.showEpub()
  }
}
</script>

<style lang='scss' scoped>
@import "~@/assets/styles/global";
.ebook {
  position: relative;
  .read-wrapper {
    .mask {
      position: absolute;
      top: 0;
      left: 0;
      display: flex;
      width: 100%;
      height: 100%;
      .left {
        flex: 0 0 px2rem(100);
      }
      .center {
        flex: 1;
      }
      .right {
        flex: 0 0 px2rem(100);
      }
    }
  }
}
</style>
