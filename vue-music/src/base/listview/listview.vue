<template>
   <scroll class="listview" :data="data" ref="listview" :listenScroll="listenScroll"
   @scroll="scroll">
     <ul>
       <li v-for="group in data" class="list-group" :key="group.index" ref="listGroup">
         <h2 class="list-group-title">{{group.title}}</h2>
         <ul>
           <li v-for="item in group.items" :key="item.index" class="list-group-item">
             <img v-lazy="item.avatar" class="avatar">
             <span class="name">{{item.name}}</span>
           </li>
         </ul>
       </li>
     </ul>
     <div class="list-shortcut" @touchstart="onShortcutTouchStart" @touchmove.stop.prevent="onShortcutTouchMove">
        <ul>
          <li v-for="(item,index) in shortcutList" :key="index" class="item" :data-index="index">
              {{item}}
          </li>
        </ul>
     </div>
   </scroll>
</template>

<script type="text/ecmascript-6">
import Scroll from 'base/scroll/scroll'
import { getData } from 'common/js/dom'
const ANCHOR_HEIGHT = 18
export default {
  created() {
    this.touch = {}
    this.listenScroll = true
  },
  data() {
    return {
        scrollY: -1,
      currentIndex: 0
    }
  },
  props: {
    data: {
      typr: Array,
      default: []
    }
  },
  computed: {
    shortcutList() {
      return this.data.map((group) => {
        return group.title.substr(0, 1)
      })
    }
  },
  methods: {
    onShortcutTouchStart(e) {
      /* 点击的索引 */
      let anchorIndex = getData(e.target, 'index')
      /* 开始手指 */
      let firstTouch = e.touches[0]
      /* 手指页面位子 */
      this.touch.y1 = firstTouch.pageY
      /* 记录index */
      this.touch.anchorIndex = anchorIndex
      /* 调用方法scrollToElement */
      this._scrollTo(anchorIndex)
    },
    onShortcutTouchMove(e) {
      let firstTouch = e.touches[0]
      this.touch.y2 = firstTouch.pageY
      /* 滑动距离 */
      let delta = (this.touch.y2 - this.touch.y1) / ANCHOR_HEIGHT | 0
      /* 开始的index加上滑过的index */
      let anchorIndex = parseInt(this.touch.anchorIndex) + delta
      /* 重新开始调用方法 */
      this._scrollTo(anchorIndex)
    },
    listenScroll() {

    },
    scroll(pos) {
      this.scrollY = pos.y
    },
    _scrollTo(index) {
      this.$refs.listview.scrollToElement(this.$refs.listGroup[index], 0)
    }
  },
  components: {
    Scroll
  }
}
</script>

<style scoped lang="stylus" rel="stylesheet/stylus" type="text/stylus">
  @import '~common/stylus/variable';

  .listview
    position: relative;
    width: 100%;
    height: 100%;
    overflow: hidden;
    background: $color-background;
    .list-group
      padding-bottom: 30px;
      .list-group-title
        height: 30px;
        line-height: 30px;
        padding-left: 20px;
        font-size: $font-size-small;
        color: $color-text-l;
        background: $color-highlight-background;
      .list-group-item
        display: flex;
        align-items: center;
        padding: 20px 0 0 30px;
        .avatar
          width: 50px;
          height: 50px;
          border-radius: 50%;
        .name
          margin-left: 20px;
          color: $color-text-l;
          font-size: $font-size-medium;
    .list-shortcut
      position: absolute;
      z-index: 30;
      right: 0;
      top: 50%;
      transform: translateY(-50%);
      width: 20px;
      padding: 20px 0;
      border-radius: 10px;
      text-align: center;
      background: $color-background-d;
      font-family: Helvetica;
      .item
        padding: 3px;
        line-height: 1;
        color: $color-text-l;
        font-size: $font-size-small;
        &.current
          color: $color-theme;
    .list-fixed
      position: absolute;
      top: 0;
      left: 0;
      width: 100%;
      .fixed-title
        height: 30px;
        line-height: 30px;
        padding-left: 20px;
        font-size: $font-size-small;
        color: $color-text-l;
        background: $color-highlight-background;
    .loading-container
      position: absolute;
      width: 100%;
      top: 50%;
      transform: translateY(-50%);
</style>
