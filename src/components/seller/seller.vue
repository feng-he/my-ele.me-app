<template>
  <div class="seller" v-el:seller>
    <div class="seller-content">
      <div class="overview">
        <h1 class="title">{{seller.name}}</h1>
        <div class="desc border-1px">
          <star :size="36" :score="seller.score"></star>
          <span class="count">({{seller.ratingCount}})</span>
          <span class="count">月售{{seller.sellCount}}单</span>
        </div>
        <ul class="remark">
          <li class="block">
            <h2 class="caption">起送价</h2>
            <div class="content">
              <span class="stress">{{seller.minPrice}}</span>元
            </div>
          </li>
          <li class="block">
            <h2 class="caption">商家配送</h2>
            <div class="content">
              <span class="stress">{{seller.deliveryPrice}}</span>元
            </div>
          </li>
          <li class="block">
            <h2 class="caption">平均配送时间</h2>
            <div class="content">
              <span class="stress">{{seller.deliveryTime}}</span>元
            </div>
          </li>
        </ul>
        <div class="favorite" @click="toggleFavorite">
          <div class="icon-favorite" :class="{'active':favorite}"></div>
          <div class="text" :class="{'active':favorite}">{{favoriteText}}</div>
        </div>
      </div>
      <split></split>
      <div class="bulletin">
        <h1 class="title">公告及活动</h1>
        <p class="content border-1px">{{seller.bulletin}}</p>
        <ul class="supports" v-if="seller.supports">
          <li class="support-item border-1px" v-for="item in seller.supports">
            <icon :postfix="4" :tp="item.type"></icon>
            <span class="text">{{item.description}}</span>
          </li>
        </ul>
      </div>
      <split></split>
      <div class="pics">
        <h1 class="title">商家实景</h1>
        <div class="pic-wrapper" v-el:pic-wrapper>
          <ul class="pic-list" v-el:pic-list>
            <li class="pic-item" v-for="pic in seller.pics">
              <img :src="pic" width="120" height="90">
            </li>
          </ul>
        </div>
      </div>
      <split></split>
      <div class="info">
        <h1 class="title border-1px">商家信息</h1>
        <ul>
          <li class="info-item border-1px" v-for="info in seller.infos">{{info}}</li>
        </ul>
      </div>
    </div>
  </div>
</template>

<script type="text/ecmascript-6">
  import BScroll from 'better-scroll'
  import star from 'components/star/star'
  import split from 'components/split/split'
  import {saveToLocal, loadFromLocal} from 'common/js/store'
  import icon from 'components/icon/icon'

  export default {
    props: {
      seller: {
        type: Object
      }
    },
    data() {
      return {
        favorite: (() => {
          return loadFromLocal(this.seller.id, 'favorite', false)
        })()
      }
    },
    computed: {
      favoriteText() {
        return this.favorite ? '已收藏' : '收藏'
      }
    },
    watch: {
      'seller'() {
        this._initScroll()
        this._initPics()
      }
    },
    ready() {
      this._initScroll()
      this._initPics()
    },
    methods: {
      _initScroll() {
        if (!this.scroll) {
          this.scroll = new BScroll(this.$els.seller, {
            click: true
          })
        } else {
          // ready执行优先级高于watch
          this.scroll.refresh()
        }
      },
      _initPics() {
        if (this.seller.pics) {
          let picWidth = 120
          let marginRight = 6
          let width = (picWidth + marginRight) * this.seller.pics.length - marginRight
          this.$els.picList.style.width = width + 'px'
          this.$nextTick(() => {
            if (!this.picScroll) {
              this.picScroll = new BScroll(this.$els.picWrapper, {
                scrollX: true,
                eventPassthrough: 'vertical'
              })
            } else {
              this.picScroll.refresh()
            }
          })
        }
      },
      toggleFavorite(event) {
        if (!event._constructed) {
          return
        }
        this.favorite = !this.favorite
        saveToLocal(this.seller.id, 'favorite', this.favorite)
      }
    },
    components: {
      star,
      split,
      icon
    }
  }
</script>

<style lang="stylus" ref="stylesheet/stylus">
  @import '../../common/stylus/mixin.styl'

  .seller
    position: absolute
    top: 175px
    left: 0
    bottom: 0
    width: 100%
    overflow: hidden
    .overview
      position: relativeicon
      padding: 18px
      .title
        margin-bottom: 8px
        font-size: 14px
        line-height: 14px
        color: rgb(7, 17, 27)
      .desc
        padding-bottom: 18px
        font-size: 0
        border-1px(rgba(7, 17, 27, 0.1))
        .star, .count
          display: inline-block
          vertical-align: top
        .star
          margin-right: 8px
        .count
          margin-right: 12px
          font-size: 10px
          line-height: 18px
          color: rgb(77, 85, 93)
      .remark
        display: flex
        padding-top: 18px
        .block
          flex: 1
          font-size: 10px
          text-align: center
          border-right: 1px solid rgba(7, 17, 27, 0.1)
          &:last-child
            border-right: none
          .caption
            margin-bottom: 4px
            line-height: 10px
            color: rgb(147, 153, 159)
          .content
            line-height: 24px
            color: rgb(7, 17, 27)
            vertical-align: bottom
            .stress
              font-size: 24px
      .favorite
        position: absolute
        top: 18px
        right: 11px
        width: 50px
        color: #d4d6d9
        text-align: center
        .icon-favorite
          margin-bottom: 4px
          font-size: 24px
          line-height: 24px
          &.active
            color: rgb(240, 20, 20)
        .text
          font-size: 10px
          line-height: 10px
          &.active
            color: rgb(77, 85, 93)
    .bulletin
      padding: 18px 18px 0
      .title
        margin-bottom: 8px
        font-size: 14px
        line-height: 14px
        color: rgb(7, 17, 27)
      .content
        padding: 0 12px 16px
        font-size: 12px
        line-height: 24px
        font-weight: 200
        color: rgb(240, 20, 20)
        border-1px(rgba(7, 17, 27, 0.1))
      .supports
        .support-item
          padding: 16px 12px
          font-size: 0
          border-1px(rgba(7, 17, 27, 0.1))
          &:last-child
            border-none()
          .icon
            margin-right: 6px
          .text
            font-size: 12px
            line-height: 16px
            font-weight: 200
            color: rgb(7, 17, 27)
    .pics
      padding: 18px
      .title
        margin-bottom: 12px
        font-size: 14px
        line-height: 14px
        color: rgb(7, 17, 27)
      .pic-wrapper
        width: 100%
        overflow: hidden
        white-space: nowrap
        .pic-list
          font-size: 0
          .pic-item
            display: inline-block
            margin-right: 6px
            width: 120px
            height: 90px
            &:last-child
              margin-right: 0
    .info
      padding: 18px 18px 0
      color: rgb(7, 17, 27)
      .title
        padding-bottom: 12px
        font-size: 14px
        line-height: 14px
        border-1px(rgba(7, 17, 27, 0.1))
      .info-item
        padding 16px 12px
        font-size: 12px
        line-height: 16px
        font-weight: 200
        border-1px(rgba(7, 17, 27, 0.1))
        &:last-child
          border-none()
</style>
