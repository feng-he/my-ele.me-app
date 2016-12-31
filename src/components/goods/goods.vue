<template>
  <div class="goods">
    <div class="menu-wrapper" v-el:menu-wrapper>
      <ul>
        <li v-for="item in goods" class="menu-item" :class="{'current':currentIndex===$index}"
            @click="selectMenu($index,$event)">
          <span class="text border-1px">
            <span class="icon" v-show="item.type>0" :class="classMap[item.type]"></span>{{item.name}}
          </span>
        </li>
      </ul>
    </div>
    <div class="foods-wrapper" v-el:foods-wrapper>
      <ul>
        <li v-for="item in goods" class="food-list food-list-hook">
          <h1 class="title">{{item.name}}</h1>
          <ul>
            <li v-for="food in item.foods" class="food-item border-1px" @click="selectFood(food,$event)">
              <div class="icon">
                <img width="57" height="57" :src="food.icon">
              </div>
              <div class="content">
                <h2 class="name">{{food.name}}</h2>
                <p v-show="food.description" class="description">{{food.description}}</p>
                <div class="extra">
                  <span class="sellCount">月售{{food.sellCount}}</span>
                  <span class="rating">好评率{{food.rating}}%</span>
                </div>
                <div class="price">
                  <span class="now">¥{{food.price}}</span>
                  <span v-if="food.oldPrice" class="old">¥{{food.oldPrice}}</span>
                </div>
              </div>
              <div class="cartcontrol-wrapper">
                <cartcontrol :food="food"></cartcontrol>
              </div>
            </li>
          </ul>
        </li>
      </ul>
    </div>
  </div>
  <shopcart v-ref:shopcart :select-foods="selectFoods" :delivery-price="seller.deliveryPrice"
            :min-price="seller.minPrice"></shopcart>
  <food :food="selectedFood" v-ref:food></food>
</template>

<script type="text/ecmascript-6">
  import BScroll from 'better-scroll'
  import shopcart from 'components/shopcart/shopcart'
  import cartcontrol from 'components/cartcontrol/cartcontrol'
  import food from 'components/food/food'

  const ERR_OK = 0

  export default {
    props: {
      seller: {
        type: Object
      }
    },
    data() {
      return {
        goods: [],
        listHeight: [],
        scrollY: 0,
        selectedFood: {}
      }
    },
    computed: {
      currentIndex() {
        for (let i = 0; i < this.listHeight.length; i++) {
          let height1 = this.listHeight[i]
          let height2 = this.listHeight[i + 1]
          if (!height2 || (this.scrollY >= height1 && this.scrollY < height2)) {
            return i
          }
        }
        return 0
      },
      selectFoods() {
        let foods = []
        this.goods.forEach((good) => {
          good.foods.forEach((food) => {
            if (food.count) {
              foods.push(food)
            }
          })
        })
        return foods
      }
    },
    created() {
      this.$http.get('/api/goods').then((response) => {
        response = response.body
        if (response.errno === ERR_OK) {
          this.goods = response.data
          this.$nextTick(() => {
            this._initScroll()
            this._calculateHeight()
          })
        }
      })
      this.classMap = ['decrease', 'discount', 'special', 'invoice', 'guarantee']
    },
    methods: {
      _initScroll() {
        this.menuSroll = new BScroll(this.$els.menuWrapper, {
          click: true
        })
        this.foodsScroll = new BScroll(this.$els.foodsWrapper, {
          click: true,
          probeType: 3
        })
        this.foodsScroll.on('scroll', (pos) => {
          this.scrollY = Math.abs(Math.round(pos.y))
        })
      },
      _calculateHeight() {
        let foodList = this.$els.foodsWrapper.getElementsByClassName('food-list-hook')
        let height = 0
        this.listHeight.push(height)
        for (let i = 0; i < foodList.length; i++) {
          let item = foodList[i]
          height += item.clientHeight
          this.listHeight.push(height)
        }
      },
      selectMenu(index, event) {
        if (!event._constructed) {
          return
        }
        let foodList = this.$els.foodsWrapper.getElementsByClassName('food-list-hook')
        let el = foodList[index]
        this.foodsScroll.scrollToElement(el, 300)
      },
      _drop(target) {
        this.$refs.shopcart.drop(target)
      },
      selectFood(food, event) {
        if (!event._constructed) {
          return
        }
        this.selectedFood = food
        this.$refs.food.show()
      }
    },
    components: {
      shopcart,
      cartcontrol,
      food
    },
    events: {
      'cart.add'(target) {
        this._drop(target)
      }
    }
  }
</script>

<style lang="stylus" ref="stylesheet/stylus">
  @import '../../common/stylus/mixin.styl'

  .goods
    display: flex
    position: absolute
    top: 175px
    left: 0
    bottom: 46px
    width: 100%
    overflow: hidden
    .menu-wrapper
      flex: 0 0 80px
      width: 80px
      background: #f3f5f7
      .menu-item
        display: table
        width: 56px
        height: 54px
        padding: 0 12px
        &.current
          position: relative
          margin-top: -1px
          z-index: 10
          background: #fff
          font-weight: 700
          .text
            border-none()
        .icon
          display: inline-block
          vertical-align: top
          width: 12px
          height: 12px
          margin-right: 2px
          background-size: 12px 12px
          background-repeat: no-repeat
          &.decrease
            bg-image('decrease_3')
          &.discount
            bg-image('discount_3')
          &.guarantee
            bg-image('guarantee_3')
          &.invoice
            bg-image('invoice_3')
          &.special
            bg-image('special_3')
        .text
          display: table-cell
          vertical-align: middle
          font-size: 12px
          line-height: 14px
          border-1px(rgba(7, 17, 27, 0.1))
    .foods-wrapper
      flex: 1
      .title
        padding-left: 14px
        width: 100%
        height: 26px
        border-left: 2px solid #d9dde1
        background: #f3f5f7
        font-size: 12px
        line-height: 26px
        color: rgb(147, 153, 159)
      .food-item
        display: flex
        position: relative
        margin: 18px 18px 0
        padding-bottom: 18px
        border-1px(rgba(7, 17, 27, 0.1))
        &:last-child
          border-none()
        .icon
          flex: 0 0 57px
          margin-right: 10px
        .content
          position: relative
          flex: 1
          .name
            margin: 2px 0 8px
            font-size: 14px
            line-height: 14px
            color: rgb(7, 17, 27)
          .description
            margin-bottom: 8px
            font-size: 10px
            line-height: 14px
            color: rgb(147, 153, 159)
          .extra
            font-size: 0
            .sellCount, .rating
              font-size: 10px
              line-height: 10px
              color: rgb(147, 153, 159)
            .rating
              margin-left: 12px
          .price
            margin-top: 2px
            font-weight: 700
            line-height: 24px
            font-size: 0
            .now
              margin-right: 8px
              font-size: 14px
              color: rgb(240, 20, 20)
            .old
              font-size: 10px
              color: rgb(147, 153, 159)
              text-decoration: line-through
        .cartcontrol-wrapper
          position: absolute
          right: 0
          bottom: 12px
</style>
