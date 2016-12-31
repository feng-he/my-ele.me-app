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
        this.$dispatch('ratingtype.select', type)
      },
      toggleContent(event) {
        if (!event._constructed) {
          return
        }
        this.onlyContent = !this.onlyContent
        this.$dispatch('content.toggle', this.onlyContent)
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

</style>
