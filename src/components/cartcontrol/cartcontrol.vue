<template>
  <div class="cartcontrol">
    <div class="remove" v-show="food.count>0" @click="removeCart" transition="move">
      <span class="inner icon-remove_circle_outline"></span>
    </div>
    <div class="count" v-show="food.count>0" transition="show">{{food.count}}</div>
    <div class="add icon-add_circle" @click="addCart"></div>
  </div>
</template>

<script type="text/ecmascript-6">
  import Vue from 'vue'

  export default {
    props: {
      food: {
        type: Object
      }
    },
    methods: {
      addCart(event) {
        if (!event._constructed) {
          return
        }
        if (!this.food.count) {
          Vue.set(this.food, 'count', 1)
        } else {
          this.food.count++
        }
      },
      removeCart(event) {
        if (!event._constructed) {
          return
        }
        if (this.food.count) {
          this.food.count--
        }
      }
    }
  }
</script>

<style lang="stylus" rel="stylesheet/stylus">
  .cartcontrol
    font-size: 0
    .remove
      display: inline-block
      vertical-align: top
      padding: 6px
      transition: all 0.4s linear
      &.move-transition
        opacity: 1
        transform: translate3d(0, 0, 0)
        .inner
          display: block
          font-size: 24px
          line-height: 24px
          color: rgb(0, 160, 220)
          transition: all 0.4s linear
          transform: rotate(0)
      &.move-enter, &.move-leave
        opacity: 0
        transform: translate3d(24px, 0, 0)
        .inner
          transform: rotate(180deg)
    .count
      display: inline-block
      vertical-align: top
      width: 12px
      font-size: 10px
      line-height: 36px
      color: rgb(147, 153, 159)
      text-align: center
      transition: all 0.4s linear
      &.show-transition
        opacity: 1
      &.show-enter, &.show-leave
        opacity: 0
    .add
      display: inline-block
      vertical-align: top
      padding: 6px
      font-size: 24px
      line-height: 24px
      color: rgb(0, 160, 220)
</style>
