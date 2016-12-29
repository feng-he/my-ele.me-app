<template>
  <div class="ratingselect">
    <div class="rating-type border-1px">
      <span class="tag positive" :class="{'active':selectType===2}" @click="select(2,$event)">{{tag.all}}<span
        class="count">{{ratings.length}}</span></span>
      <span class="tag positive" :class="{'active':selectType===0}" @click="select(0,$event)">{{tag.positive}}<span
        class="count">{{positives.length}}</span></span>
      <span class="tag negative" :class="{'active':selectType===1}" @click="select(1,$event)">{{tag.negative}}<span
        class="count">{{negatives.length}}</span></span>
    </div>
    <div class="switch border-1px" :class="{'on':onlyContent}" @click="toggleContent">
      <span class="icon-check_circle"></span>
      <span class="text">只看有内容的评价</span>
    </div>
    <div class="rating-wrapper" v-for="rating in ratings">
      <ul v-show="ratings && ratings.length">
        <li class="rating-item border-1px" v-show="ratingShow(rating.rateType,rating.text)">
          <div class="user">
            <span class="time">{{rating.rateTime}}</span>
            <span class="username">{{rating.username}}</span>
            <img class="avatar" width="12" height="12" :src="rating.avatar">
          </div>
          <div class="rating-content" @click="toggleContent">
            <span class="thumb"
                  :class="{'icon-thumb_up':rating.rateType===0,'icon-thumb_down':rating.rateType===1}"></span>
            <span class="text">{{rating.text}}</span>
          </div>
        </li>
      </ul>
      <div class="no-rating" v-show="!ratings || !ratings.length">暂无评价</div>
    </div>
  </div>
</template>

<script type="text/ecmascript-6">
  const POSITIVE = 0
  const NEGATIVE = 1
  const ALL = 2

  export default {
    props: {
      ratings: {
        type: Array,
        default() {
          return []
        }
      },
      selectType: {
        type: Number,
        default: ALL
      },
      onlyContent: {
        type: Boolean,
        default: false
      },
      tag: {
        type: Object,
        default() {
          return {
            all: '全部',
            positive: '满意',
            negative: '不满意'
          }
        }
      }
    },
    computed: {
      positives() {
        return this.ratings.filter((rating) => {
          return rating.rateType === POSITIVE
        })
      },
      negatives() {
        return this.ratings.filter((rating) => {
          return rating.rateType === NEGATIVE
        })
      }
    },
    created() {
      this.thumbMap = ['icon-thumb_up', 'icon-thumb_down']
    },
    methods: {
      select(type, event) {
        if (!event._constructed) {
          return
        }
        this.selectType = type
        this.$dispatch('ratingtype.selcet', type)
      },
      toggleContent(event) {
        if (!event._constructed) {
          return
        }
        this.onlyContent = !this.onlyContent
        this.$dispatch('content.toggle', this.onlyContent)
      },
      ratingShow(type, text) {
        if (this.onlyContent && !text) {
          return false
        }
        if (this.selectType === ALL) {
          return true
        } else {
          return type === this.selectType
        }
      }
    }
  }
</script>

<style lang="stylus" rel="stylesheet/stylus">
  @import '../../common/stylus/mixin.styl'

  .ratingselect
    .rating-type
      margin: 0 18px
      padding: 18px 0
      border-1px(rgba(7, 17, 27, 0.1))
      font-size: 0
      .tag
        display: inline-block
        padding: 8px 12px
        margin-right: 8px
        border-radius: 1px
        font-size: 12px
        line-height: 16px
        color: rgb(77, 85, 93)
        .count
          font-size: 8px
          margin-left: 2px
        &.positive
          background: rgba(0, 160, 220, 0.2)
          &.active
            background: rgb(0, 160, 220)
            color: #fff
        &.negative
          background: rgba(77, 85, 93, 0.2)
          &.active
            background: rgb(77, 85, 93)
            color: #fff
    .switch
      padding: 12px 18px
      font-size: 0
      line-height: 24px
      color: rgb(147, 153, 159)
      border-1px(rgba(7, 17, 27, 0.1))
      &.on
        color: #00c850
      .icon-check_circle
        display: inline-block
        vertical-align: top
        margin-right: 4px
        font-size: 24px
      .text
        display: inline-block
        vertical-align: top
        font-size: 12px
    .rating-wrapper
      padding: 0 18px
      .rating-item
        position: relative
        padding: 16px 0
        border-1px(rgba(7, 17, 27, 0.1))
        .user
          height: 12px
          font-size: 10px
          line-height: 12px
          color: rgb(147, 153, 159)
          .time
            position: absolute
            top: 16px
            left: 0
          .username
            position: absolute
            top: 16px
            right: 18px
          .avatar
            position: absolute
            top: 16px
            right: 0
            border-radius: 50%
        .rating-content
          margin-top: 6px
          font-size: 0
          .thumb
            display: inline-block
            vertical-align: top
            margin-right: 4px
            font-size: 12px
            line-height: 24px
            &.icon-thumb_up
              color: rgb(0, 160, 220)
            &.icon-thumb_down
              color: rgb(147, 153, 159)
          .text
            display: inline-block
            vertical-align: top
            font-size: 12px
            line-height: 24px
            color: rgb(7, 17, 27)
</style>
