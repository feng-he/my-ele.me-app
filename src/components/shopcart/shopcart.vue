<template>
  <div class="shopcart">
    <div class="content-left">
      <div class="logo-wrapper">
        <div class="logo icon-shopping_cart" :class="{'highlight':totalCount>0}">
        </div>
        <div class="count" v-show="totalCount>0">{{totalCount}}</div>
      </div>
      <div class="total-price" :class="{'highlight':totalCount>0}">¥{{totalPrice}}</div>
      <div class="delivery-price">另需配送费￥{{deliveryPrice}}元</div>
    </div>
    <div class="content-right">
      <div class="amount-to-delivery" :class="{'highlight':totalPrice>=minPrice}">{{priceDifference}}</div>
    </div>
  </div>
</template>

<script type="text/ecmascript-6">
  export default {
    props: {
      selectFoods: {
        type: Array,
        default() {
          return [{
            price: 10,
            count: 1
          }]
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
      }
    }
  }
</script>

<style lang="stylus" rel="stylesheet/stylus">
  .shopcart
    display: flex
    position: fixed
    left: 0
    bottom: 0
    width: 100%
    height: 48px
    z-index: 50
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
</style>
