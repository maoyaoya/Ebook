<template>
  <div class='ebook'>
    <div class='read-wrapper'>
      <div id='read'></div>
    </div>
  </div>
</template>

<script>
import Epub from 'epubjs'
const DOWNLOAD_URL = '/static/2018_Book_AgileProcessesInSoftwareEngine.epub'
global.epub = Epub
/* eslint-disable space-before-function-paren */
export default {
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
    }
  },
  mounted() {
    this.showEpub()
  }
}
</script>

<style lang='scss' scoped>
@import "~@/assets/styles/global";
</style>
