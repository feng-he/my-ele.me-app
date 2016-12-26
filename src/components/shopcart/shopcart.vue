<template>
  <div class="shopcart">
    <div class="content">
      <div class="content-left" @click="toggleList">
        <div class="logo-wrapper">
          <div class="logo icon-shopping_cart" :class="{'highlight':totalCount>0}">
          </div>
          <div class="count" v-show="totalCount>0">{{totalCount}}</div>
        </div>
        <div class="total-price" :class="{'highlight':totalCount>0}">¥{{totalPrice}}</div>
        <div class="delivery-price">另需配送费￥{{deliveryPrice}}元</div>
      </div>
      <div class="content-right" @click="pay">
        <div class="amount-to-delivery" :class="{'highlight':totalPrice>=minPrice}">{{priceDifference}}</div>
      </div>
    </div>
    <div class="shopcart-list" v-show="listShow" transition="fold">
      <div class="list-header">
        <h1 class="title">购物车</h1>
        <span class="empty" @click="empty">清空</span>
      </div>
      <div class="list-content" v-el:list-content>
        <ul>
          <li class="food" v-for="food in selectFoods">
            <span class="name">{{food.name}}</span>
            <div class="cartcontrol-wrapper">
              <cartcontrol :food="food"></cartcontrol>
            </div>
            <span class="price">¥{{food.price*food.count}}</span>
          </li>
        </ul>
      </div>
    </div>
    <div class="ball-container">
      <div class="ball" v-for="ball in balls" v-show="ball.show" transition="drop">
        <div class="inner inner-hook"></div>
      </div>
    </div>
  </div>
  <div class="list-mask" v-show="listShow" transition="fade" @click="toggleList"></div>
</template>

<script type="text/ecmascript-6">
  import cartcontrol from 'components/cartcontrol/cartcontrol'
  import BScroll from 'better-scroll'

  export default {
    props: {
      selectFoods: {
        type: Array,
        default() {
          return []
        }
      },
      deliveryPrice: {
        type: Number,
        default: 0
      },
      minPrice: {
        type: Number,
        default: 0
      }
    },
    data() {
      return {
        fold: true,
        balls: [
          {
            show: false
          },
          {
            show: false
          },
          {
            show: false
          },
          {
            show: false
          },
          {
            show: false
          },
          {
            show: false
          }
        ],
        dropBalls: []
      }
    },
    computed: {
      totalPrice() {
        let price = 0
        this.selectFoods.forEach((food) => {
          price += food.price * food.count
        })
        return price
      },
      totalCount() {
        let count = 0
        this.selectFoods.forEach((food) => {
          count += food.count
        })
        return count
      },
      priceDifference() {
        if (this.totalPrice === 0) {
          return `￥${this.minPrice}起送`
        } else if (this.totalPrice < this.minPrice) {
          let diff = this.minPrice - this.totalPrice
          return `还差￥${diff}起送`
        } else {
          return '去结算'
        }
      },
      listShow() {
        if (!this.totalCount) {
          this.fold = true
          return false
        }
        let show = !this.fold
        if (show) {
          this.$nextTick(() => {
            if (!this.scroll) {
              this.scroll = new BScroll(this.$els.listContent, {
                click: true
              })
            } else {
              this.scroll.refresh()
            }
          })
        }
        return show
      }
    },
    methods: {
      toggleList() {
        if (!this.totalCount) {
          return
        }
        this.fold = !this.fold
      },
      empty() {
        this.selectFoods.forEach((food) => {
          food.count = 0
        })
      },
      pay() {
        if (this.totalPrice < this.minPrice) {
          return
        }
        window.alert(`请支付${this.totalPrice}元`)
      },
      drop(el) {
        for (let i = 0; i < this.balls.length; i++) {
          let ball = this.balls[i]
          if (!ball.show) {
            ball.show = true
            ball.el = el
            this.dropBalls.push(ball)
            return
          }
        }
      }
    },
    components: {
      cartcontrol
    },
    transitions: {
      drop: {
        beforeEnter(el) {
          this.dropBalls.forEach((ball) => {
            let rect = ball.el.getBoundingClientRect()
            let x = rect.left - 32
            let y = -(window.innerHeight - rect.top - 22)
            el.style.display = ''
            el.style.webkitTransform = `translate3d(0,${y}px,0)`
            el.style.transform = `translate3d(0,${y}px,0)`
            let inner = el.getElementsByClassName('inner-hook')[0]
            inner.style.webkitTransform = `translate3d(${x}px,0,0)`
            inner.style.transform = `translate3d(${x}px,0,0)`
          })
        },
        enter(el) {
          /* eslint-disable no-unused-vars */
          let rf = el.offsetHeight
          this.$nextTick(() => {
            el.style.webkitTransform = 'translate3d(0,0,0)'
            el.style.transform = 'translate3d(0,0,0)'
            let inner = el.getElementsByClassName('inner-hook')[0]
            inner.style.webkitTransform = 'translate3d(0,0,0)'
            inner.style.transform = 'translate3d(0,0,0)'
          })
        },
        afterEnter(el) {
          let ball = this.dropBalls.shift()
          if (ball) {
            ball.show = false
            el.style.display = 'none'
          }
        }
      }
    }
  }
</script>

<style lang="stylus" rel="stylesheet/stylus">
  @import '../../common/stylus/mixin.styl'

  .shopcart
    position: fixed
    left: 0
    bottom: 0
    width: 100%
    z-index: 50
    .content
      display: flex
      width: 100%
      height: 48px
      color: rgba(255, 255, 255, 0.4)
      .content-left
        flex: 1
        background: #141d27
        font-size: 0
        .logo-wrapper
          display: inline-block
          position: relative
          vertical-align: top
          margin: -10px 12px 2px
          padding: 6px
          width: 44px
          height: 44px
          background: #141d27
          border-radius: 50%
          .logo
            width: 100%
            height: 100%
            background: #2b343c
            border-radius: 50%
            text-align: center
            font-size: 24px
            line-height: 44px
            &.highlight
              background: rgb(0, 160, 220)
              color: #fff
          .count
            position: absolute
            top: 0
            right: 0
            width: 24px
            height: 16px
            border-radius: 16px
            background: rgb(240, 20, 20)
            box-shadow: 0 4px 8px rgb(0, 0, 0, 0.4)
            font-size: 9px
            line-height: 16px
            font-weight: 700
            color: #fff
            text-align: center
        .total-price
          display: inline-block
          vertical-align: top
          margin-top: 12px
          padding-right: 12px
          font-size: 16px
          line-height: 24px
          font-weight: 700
          border-right: 1px solid rgba(255, 255, 255, 0.1)
          &.highlight
            color: #fff
        .delivery-price
          display: inline-block
          vertical-align: top
          margin-left: 12px
          font-size: 10px
          line-height: 48px
      .content-right
        flex: 0 0 105px
        background: #2b333b
        .amount-to-delivery
          width: 100%
          height: 100%
          font-size: 12px
          font-weight: 700
          line-height: 48px
          text-align: center
          &.highlight
            background: #00b43c
            color: #fff
    .shopcart-list
      position: absolute
      left: 0
      top: 0
      width: 100%
      z-index: -1
      &.fold-transition
        transition: all 0.5s
        transform: translate3d(0, -100%, 0)
      &.fold-enter, &.fold-leave
        transition: all 0.5s
        transform: translate3d(0, 0, 0)
      .list-header
        width: 100%
        height: 40px
        line-height: 40px
        padding: 0 18px
        box-sizing: border-box
        background: #f3f5f7
        border-bottom: 2px solid rgba(7, 17, 27, 0.1)
        .title
          float: left
          font-size: 14px
          color: rgb(7, 17, 27)
        .empty
          float: right
          font-size: 12px
          color: rgb(0, 160, 220)
      .list-content
        max-height: 217px
        overflow: hidden
        background: #fff
        .food
          width: 100%
          height: 48px
          padding: 12px 18px
          box-sizing: border-box
          border-1px(rgba(7, 17, 27, 0.1))
          .name
            float: left
            font-size: 14px
            line-height: 24px
            color: rgb(7, 17, 27)
          .price
            float: right
            margin-right: 6px
            font-size: 14px
            line-height: 24px
            color: rgb(240, 20, 20)
            font-weight: 700
          .cartcontrol-wrapper
            float: right
            margin-top: -6px
            margin-right: -6px
    .ball-container
      .ball
        position: fixed
        left: 32px
        bottom: 22px
        z-index: 200
        &.drop-transition
          transition: all 0.4s cubic-bezier(0.5, -0.3, 0.75, 0.4)
          .inner
            width: 16px
            height: 16px
            border-radius: 50%
            background: rgb(0, 160, 220)
            transition: all 0.4s
  .list-mask
    position: fixed
    top: 0
    left: 0
    width: 100%
    height: 100%
    z-index: 40
    backdrop: blur(10px)
    &.fade-transition
      transition: all 0.5s
      opacity: 1
      background: rgba(7, 17, 27, 0.6)
    &.fade-enter, &.fade-leave
      opacity: 0
      background: rgba(7, 17, 27, 0)
</style>
