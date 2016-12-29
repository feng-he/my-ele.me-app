<template>
  <div class="food" v-show="showFlag" transition="move" v-el:food-scroll>
    <div class="food-scroll">
      <div class="image-wrapper">
        <img :src="food.image">
        <i class="back icon-arrow_lift" @click="back"></i>
      </div>
      <div class="content">
        <h1 class="name">{{food.name}}</h1>
        <div class="detail">
          <span class="sell-count">月售{{food.sellCount}}份</span>
          <span class="rating">好评率{{food.rating}}%</span>
        </div>
        <div class="price">
          <span class="new">￥{{food.price}}</span>
          <span class="old" v-if="food.oldPrice ">￥{{food.oldPrice}}</span>
        </div>
        <div class="cartcontrol-wrapper">
          <cartcontrol :food="food"></cartcontrol>
        </div>
        <div class="buy" v-show="!food.count||food.count===0" @click.stop.prevent="buy" transition="disappear">加入购物车</div>
      </div>
      <split v-if="food.info"></split>
      <div class="info" v-if="food.info">
        <h1 class="title">商品简介</h1>
        <div class="text">{{food.info}}</div>
      </div>
      <split v-if="food.ratings"></split>
      <div class="ratings">
        <h1 class="title">商品评价</h1>
        <ratingselect :select-type="selectType" :only-content="onlyContent" :tag="tag" :ratings="food.ratings"></ratingselect>
      </div>
    </div>
  </div>
</template>

<script type="text/ecmascript-6">
  import BScroll from 'better-scroll'
  import cartcontrol from 'components/cartcontrol/cartcontrol'
  import Vue from 'vue'
  import split from 'components/split/split'
  import ratingselect from 'components/ratingselect/ratingselect'

  // const POSITIVE = 0
  // const NEGATIVE = 1
  const ALL = 2

  export default {
    props: {
      food: {
        type: Object
      }
    },
    data() {
      return {
        showFlag: false,
        selectType: ALL,
        onlyContent: false,
        tag: {
          all: '全部',
          positive: '推荐',
          negative: '吐槽'
        }
      }
    },
    methods: {
      show() {
        this.showFlag = true
        this.selectType = ALL
        this.onlyContent = false
        this.$nextTick(() => {
          if (!this.scroll) {
            this.scroll = new BScroll(this.$els.foodScroll, {
              click: true
            })
          } else {
            this.scroll.refresh()
          }
        })
      },
      back() {
        this.showFlag = false
      },
      buy(event) {
        if (!event._constructed) {
          return
        }
        Vue.set(this.food, 'count', 1)
        this.$dispatch('cart.add', event.target)
      }
    },
    components: {
      cartcontrol,
      split,
      ratingselect
    }
  }
</script>

<style lang="stylus" rel="stylesheet/stylus">
  .food
    position: fixed
    top: 0
    left: 0
    bottom: 48px
    width: 100%
    z-index: 30
    background: #fff
    &.move-transition
      transition: all 0.2s linear
      transform: translate3d(0, 0, 0)
    &.move-enter, &.move-leave
      transform: translate3d(100%, 0, 0)
    .image-wrapper
      position: relative
      width: 100%
      height: 0
      padding-top: 100%
      img
        position: absolute
        top: 0
        left: 0
        width: 100%
        height: 100%
      .back
        position: absolute
        top: 10px
        left: 5px
        font-size: 14px
        padding: 10px
        color: #fff
        z-index: 100
    .content
      position: relative
      padding: 18px
      .name
        font-size: 14px
        line-height: 14px
        color: rgb(7, 17, 27)
        font-weight: 700
      .detail
        margin-top: 8px
        height: 10px
        font-size: 0px
        line-height: 10px
        color: rgb(147, 153, 159)
        .sell-count
          margin-right: 12px
          font-size: 10px
        .rating
          font-size: 10px
      .price
        margin-top: 18px
        font-size: 0
        line-height: 24px
        font-weight: 700
        .new
          margin-right: 8px
          font-size: 14px
          color: rgb(240, 20, 20)
        .old
          font-size: 10px
          color: rgb(147, 153, 159)
          text-decoration: line-through
      .cartcontrol-wrapper
        position: absolute
        right: 12px
        bottom: 12px
      .buy
        position: absolute
        right: 18px
        bottom: 18px
        height: 24px
        border-radius: 12px
        padding: 0 12px
        box-sizing: border-box
        background: rgb(0, 160, 220)
        font-size: 10px
        line-height: 24px
        color: #fff
        text-align: center
        z-index: 10
        &.disappear-transition
          transition: all 0.2s
          opacity: 1
        &.disappear-enter, &.disappear-leave
          opacity: 0
    .info
      padding: 18px
      .title
        margin-bottom: 6px
        font-size: 14px
        line-height: 14px
        color: rgb(7, 17, 27)
      .text
        padding: 0 8px
        font-size: 12px
        line-height: 24px
        color: rgb(77, 85, 93)
    .ratings
      .title
        padding: 18px 18px 0
        font-size: 14px
        line-height: 12px
        color: rgb(77, 85, 93)

</style>
