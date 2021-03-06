<template>
  <div class="ebook">
    <title-bar :ifTitleAndMenuShow="ifTitleAndMenuShow"></title-bar>
    <div class="read-wrapper">
      <div id="read"></div>
      <div class="mask">
        <div class="left" @click="prevPage"></div>
        <div class="center" @click="toggleTitleAndMenu"></div>
        <div class="right" @click="nextPage"></div>
      </div>
    </div>
    <menu-bar
      :ifTitleAndMenuShow="ifTitleAndMenuShow"
      :fontSizeList="fontSizeList"
      :defaultFontSize="defaultFontSize"
      @setFontSize="setFontSize"
      :themeList="themeList"
      :defaultTheme="defaultTheme"
      @setTheme="setTheme"
      :bookAvailable="bookAvailable"
      @onProgressChange="onProgressChange"
      :navigation="navigation"
      @jumpTo="jumpTo"
      ref="menuBar"
    ></menu-bar>
  </div>
</template>

<script>
import TitleBar from '@/components/TitleBar'
import MenuBar from '@/components/MenuBar'
import Epub from 'epubjs'
const DOWNLOAD_URL = './static/renleijianshi.epub'
export default {
  components: {
    TitleBar,
    MenuBar,
  },
  data() {
    return {
      ifTitleAndMenuShow: false,
      fontSizeList: [
        { fontSize: 12 },
        { fontSize: 14 },
        { fontSize: 16 },
        { fontSize: 18 },
        { fontSize: 20 },
        { fontSize: 22 },
        { fontSize: 24 },
      ],
      defaultFontSize: 16,
      themeList: [
        {
          name: 'default',
          style: {
            body: {
              color: '#000',
              background: '#fff',
            },
          },
        },
        {
          name: 'green',
          style: {
            body: {
              color: '#000',
              background: '#ceeaba',
            },
          },
        },
        {
          name: 'night',
          style: {
            body: {
              color: '#fff',
              background: '#000',
            },
          },
        },
        {
          name: 'gold',
          style: {
            body: {
              color: '#000',
              background: 'rgb(241, 236, 226)',
            },
          },
        },
      ],
      defaultTheme: 0,
      // 图书是否处于可用状态
      bookAvailable: false,
      navigation: {},
      themes: null,
    }
  },
  watch: {
    defaultTheme(val) {
      this.setTheme(val)
    },
  },
  methods: {
    // 根据链接跳转到指定位置
    jumpTo(href) {
      this.rendition.display(href)
      this.hideTitleAndMenu()
    },
    hideTitleAndMenu() {
      // 隐藏标题栏和菜单栏
      this.ifTitleAndMenuShow = false
      // 隐藏菜单栏弹出的设置栏
      this.$refs.menuBar.hideSetting()
      // 隐藏目录
      this.$refs.menuBar.hideContent()
    },
    // progress 进度条的数值（0-100）
    onProgressChange(progress) {
      const percentage = progress / 100
      const location =
        percentage > 0 ? this.locations.cfiFromPercentage(percentage) : 0
      this.rendition.display(location)
    },
    // 设置主题
    // setTheme(index) {
    //   // console.log(index, this.themeList[index].name)
    //   this.themes.select(this.themeList[index].name)
    //   this.defaultTheme = index
    // },
    // 设置主题,解决主题只能切换一次 https://github.com/futurepress/epub.js/issues/1101
    setTheme(index){
      const name = this.themeList[index].name
      // this.book.rendition.themes.select(name)
      const bodyObject = this.themeList[index].style.body
      for(let key in bodyObject){
        this.book.rendition.themes.override(key,bodyObject[key],true)
      }
    },
    // 注册主题
    registerTheme() {
      this.themeList.forEach((theme) => {
        this.themes.register(theme.name, theme.style)
      })
    },
    // 设置字号大小
    setFontSize(fontSize) {
      this.defaultFontSize = fontSize
      if (this.themes) {
        this.themes.fontSize(fontSize + 'px')
      }
    },
    // 切换标题和菜单的显示状态
    toggleTitleAndMenu() {
      this.ifTitleAndMenuShow = !this.ifTitleAndMenuShow
      if (!this.ifTitleAndMenuShow) {
        this.$refs.menuBar.hideSetting()
      }
    },
    // 上一页
    prevPage() {
      if (this.rendition) {
        this.rendition.prev()
      }
    },
    // 下一页
    nextPage() {
      if (this.rendition) {
        this.rendition.next()
      }
    },
    // 电子书的解析和渲染
    showEpub() {
      // 生成Book对象
      this.book = new Epub(DOWNLOAD_URL)
      // 通过Book.renderTo生成Rendition对象
      this.rendition = this.book.renderTo('read', {
        width: window.innerWidth,
        height: window.innerHeight,
        // 兼容iOS
        method: 'default',
      })
      // 通过Rendtion.display渲染电子书
      this.rendition.display()
      // 获取Theme对象
      this.themes = this.rendition.themes
      // 设置默认字体
      this.setFontSize(this.defaultFontSize)
      // 注册主题
      this.registerTheme()
      // 设置默认主题
      this.setTheme(this.defaultTheme)
      // Book对象的钩子函数ready
      this.book.ready
        .then(() => {
          this.navigation = this.book.navigation
          // 生成Locations对象
          return this.book.locations.generate()
        })
        .then(() => {
          // 保存locations对象
          this.locations = this.book.locations
          // 标记电子书为解析完毕状态
          this.bookAvailable = true
        })
    },
  },
  mounted() {
    this.showEpub()
  },
}
</script>

<style lang='scss' scoped>
@import 'assets/styles/global';
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
