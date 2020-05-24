<template>
  <div class="ebook">
    <title-bar :menuShow="menuShow"></title-bar>
    <div class="read-wrapper">
      <div id="read"></div>
      <div class="mask">
        <div class="left" @click="prevPage"></div>
        <div class="center" @click="toggleTitleMenu"></div>
        <div class="right" @click="nextPage"></div>
      </div>
    </div>
    <menu-bar :menuShow="menuShow"
    ref="menuBar"
    @setFontSize="setFontSize"
    :fontSizeList="fontSizeList"
    :defaultFontSize="defaultFontSize"
    ></menu-bar>
  </div>
</template>

<script>
import TitleBar from '@/components/TitleBar'
import MenuBar from '@/components/MenuBar'
import Epub from 'epubjs'
const EBOOK_URL = '/static/人类简史.epub'
export default {
  name: 'ebook',
  data() {
    return {
      menuShow: false,
      fontSizeList: [
        { fontSize: 12 },
        { fontSize: 14 },
        { fontSize: 16 },
        { fontSize: 18 },
        { fontSize: 20 },
        { fontSize: 22 },
        { fontSize: 24 }
      ],
      // 控制显示字体大小
      defaultFontSize: 16
    }
  },
  methods: {
    setFontSize(fontSize) {
      this.defaultFontSize = fontSize
      if (this.themes) {
        this.themes.fontSize(fontSize + 'px')
      }
    },
    toggleTitleMenu() {
      this.menuShow = !this.menuShow
      if (!this.menuShow) {
        this.$refs.menuBar.hide()
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
    // 电子书的解析与渲染
    showEpub() {
      // 生成电子书
      this.book = new Epub(EBOOK_URL)
      // 生成Rendition
      this.rendition = this.book.renderTo('read', {
        width: window.innerWidth,
        height: window.innerHeight
      })
      // 通过Rendition.display渲染电子书
      this.rendition.display()
      // 获取theme对象
      this.themes = this.rendition.themes
      // 设置字体大小
      this.setFontSize(this.defaultFontSize)
    }
  },
  mounted() {
    this.showEpub()
  },
  components: {
    TitleBar,
    MenuBar
  }
}
</script>

<style lang='scss' scoped>
@import "assets/styles/global";
.ebook {
  position: relative;
  .read-wrapper {
    .mask {
      position: absolute;
      top: 0;
      left: 0;
      z-index: 100;
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
