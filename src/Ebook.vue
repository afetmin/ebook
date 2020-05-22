<template>
  <div class="ebook">
    <transition name="slide-down">
    <div class="title-wrapper" v-show="menuShow">
      <div class="left">
        <span class="icon icon-back"></span>
      </div>
      <div class="right">
        <div class="icon-wrapper">
          <span class="icon icon-cart"></span>
        </div>
        <div class="icon-wrapper">
          <span class="icon icon-person"></span>
        </div>
        <div class="icon-wrapper">
          <span class="icon icon-more"></span>
        </div>
      </div>
    </div>
    </transition>
    <div class="read-wrapper">
      <div id="read"></div>
      <div class="mask">
        <div class="left" @click="prevPage"></div>
        <div class="center" @click="toggleTitleMenu"></div>
        <div class="right" @click="nextPage"></div>
      </div>
    </div>
    <transition name="slide-up">
    <div class="menu-wrapper" v-show="menuShow">
      <div class="icon-wrapper">
        <span class="icon icon-menu"></span>
      </div>
      <div class="icon-wrapper">
        <span class="icon icon-progress"></span>
      </div>
      <div class="icon-wrapper">
        <span class="icon icon-bright"></span>
      </div>
      <div class="icon-wrapper">
        <span class="icon icon-a">A</span>
      </div>
    </div>
    </transition>
  </div>
</template>

<script>
import Epub from 'epubjs'
const EBOOK_URL = '/static/人类简史.epub'
export default {
  name: 'ebook',
  data() {
    return {
      menuShow: false
    }
  },
  methods: {
    toggleTitleMenu() {
      this.menuShow = !this.menuShow
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
    }
  },
  mounted() {
    this.showEpub()
  }
}
</script>

<style lang='scss' scoped>
@import "assets/styles/global";
.ebook {
  position: relative;
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
      //global里面的center，使左侧居中
      @include center;
    }
    .right {
      flex: 1;
      display: flex;
      justify-content: flex-end;
      .icon-wrapper {
        flex: 0 0 px2rem(40);
        // @include center;
      }
    }
  }
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
  .menu-wrapper {
    position: absolute;
    bottom: 0;
    left: 0;
    z-index: 101;
    display: flex;
    width: 100%;
    height: px2rem(48);
    background: white;
    box-shadow: 0 px2rem(-8) px2rem(6) rgba(0, 0, 0, 0.15);
    .icon-wrapper {
      flex: 1;
      @include center;
      .icon-menu {
        font-size: px2rem(24);
      }
      .icon-progress {
        font-size: px2rem(26);
      }
    }
  }
}
</style>
