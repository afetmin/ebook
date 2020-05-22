<template>
    <div id="read"></div>
</template>

<script>
import Epub from 'epubjs'
const EBOOK_URL = '/static/人类简史.epub'
export default {
  name: 'ebook',
  methods: {
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
    }
  },
  mounted() {
    this.showEpub()
  }
}
</script>

<style lang='scss' scoped>
@import 'assets/styles/global';
</style>
