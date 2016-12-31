<template>
  <div class="ratings" v-el:ratings>
    <div class="ratings-content">
      <div class="overview">
        <div class="overview-left">
          <div class="score">{{seller.score}}</div>
          <div class="title">综合评分</div>
          <div class="rankRate">高于周边商家{{seller.rankRate}}%</div>
        </div>
        <div class="overview-right">
          <div class="score-wrapper">
            <span class="title">服务态度</span>
            <star :size="36" :score="seller.serviceScore"></star>
            <span class="score">{{seller.serviceScore}}</span>
          </div>
          <div class="score-wrapper">
            <span class="title">商品评分</span>
            <star :size="36" :score="seller.foodScore"></star>
            <span class="score">{{seller.foodScore}}</span>
          </div>
          <div class="delivery-time">
            <span class="title">送达时间</span>
            <span class="time">{{seller.deliveryTime}}分钟</span>
          </div>
        </div>
      </div>
      <split></split>
      <ratingselect :select-type="selectType" :only-content="onlyContent" :ratings="ratings"></ratingselect>
      <div class="rating-wrapper">
        <ul>
          <li class="rating-item border-1px" v-for="rating in ratings" v-show="ratingShow(rating.rateType,rating.text)">
            <div class="avatar">
              <img class="avatar" width="28" height="28" :src="rating.avatar">
            </div>
            <div class="content">
              <div class="username">{{rating.username}}</div>
              <div class="star-wrapper">
                <star :size="24" :score="rating.score"></star>
                <span class="delivery-time" v-if="rating.deliveryTime">{{rating.deliveryTime}}分钟</span>
              </div>
              <p class="text">{{rating.text}}</p>
              <div class="recommend" v-if="rating.recommend && rating.recommend.length">
                <span class="icon-thumb_up"></span>
                <span class="item" v-for="item in rating.recommend">{{item}}</span>
              </div>
              <div class="rateTime">{{rating.rateTime | formatDate}}</div>
            </div>
          </li>
        </ul>
        <div class="no-rating" v-if="!ratings.length">暂无评价</div>
      </div>
    </div>
  </div>
</template>

<script type="text/ecmascript-6">
  import BScroll from 'better-scroll'
  import split from 'components/split/split'
  import ratingselect from 'components/ratingselect/ratingselect'
  import star from 'components/star/star'
  import {formatDate} from 'common/js/date'

  const ERR_OK = 0
  const ALL = 2

  export default {
    props: {
      seller: {
        type: Object
      }
    },
    data() {
      return {
        ratings: [],
        selectType: ALL,
        onlyContent: true
      }
    },
    created() {
      this.$http.get('/api/ratings').then((response) => {
        response = response.body
        if (response.errno === ERR_OK) {
          this.ratings = response.data
          this.$nextTick(() => {
            this._initScroll()
          })
        }
      })
    },
    methods: {
      _initScroll() {
        this.scroll = new BScroll(this.$els.ratings, {
          click: true
        })
      },
      ratingShow(type, text) {
        if (this.onlyContent && !text) {
          return false
        }
        if (this.selectType === ALL) {
          return true
        } else {
          console.log(this.selectType)
          return type === this.selectType
        }
      }
    },
    components: {
      split,
      ratingselect,
      star
    },
    filters: {
      formatDate(time) {
        let date = new Date()
        return formatDate(date, 'yyyy-MM-dd hh:mm')
      }
    },
    events: {
      'ratingtype.select'(type) {
        this.selectType = type
        this.$nextTick(() => {
          this.scroll.refresh()
        })
      },
      'content.toggle'(onlyContent) {
        this.onlyContent = onlyContent
        this.$nextTick(() => {
          this.scroll.refresh()
        })
      }
    }
  }
</script>

<style lang="stylus" ref="stylesheet/stylus">
  @import '../../common/stylus/mixin.styl'

  .ratings
    position: absolute
    top: 175px
    left: 0
    bottom: 0
    width: 100%
    overflow: hidden
    .overview
      display: flex
      padding: 18px 0
      .overview-left
        flex: 0 0 137px
        padding: 6px 0
        text-align: center
        border-right: 1px solid rgba(7, 17, 27, 0.1)
        @media only screen and (max-width: 320px)
          flex: 0 0 120px
        .score
          margin-bottom: 6px
          font-size: 24px
          line-height: 28px
          color: rgb(250, 153, 0)
        .title
          margin-bottom: 8px
          font-size: 12px
          line-height: 12px
          color: rgb(7, 17, 27)
        .rankRate
          font-size: 10px
          line-height: 10px
          color: rgb(147, 153, 159)
      .overview-right
        flex: 1
        padding: 6px 0 6px 24px
        @media only screen and (max-width: 320px)
          padding-left: 6px
        .score-wrapper
          margin-bottom: 8px
          font-size: 0px
          .title
            display: inline-block
            vertical-align: top
            font-size: 12px
            line-height: 18px
            color: rgb(7, 17, 27)
          .star
            display: inline-block
            vertical-align: top
            margin: 0 12px
          .score
            display: inline-block
            vertical-align: top
            font-size: 12px
            line-height: 18px
            color: rgb(250, 153, 0)
        .delivery-time
          font-size: 0
          line-height: 18px
          .title
            font-size: 12px
            color: rgb(7, 17, 27)
          .time
            margin-left: 12px
            font-size: 12px
            color: rgb(147, 153, 159)
    .rating-wrapper
      padding: 0 18px
      .rating-item
        display: flex
        padding: 18px 0
        border-1px(rgba(7, 17, 27, 0.1))
        .avatar
          flex: 0 0 28px
          width: 28px
          margin-right: 12px
          img
            border-radius: 50%
        .content
          flex: 1
          position: relative
          .username, .delivery-time, .rateTime
            font-size: 10px
            line-height: 12px
          .delivery-time, .rateTime, .item
            color: rgb(147, 153, 159)
          .username
            margin-bottom: 4px
            color: rgb(7, 17, 27)
          .star-wrapper
            margin-bottom: 6px
            font-size: 0
            .star
              display: inline-block
              margin-right: 6px
          .text
            margin-bottom: 8px
            font-size: 12px
            line-height: 18px
            color: rgb(7, 17, 27)
          .recommend
            font-size: 0
            .icon-thumb_up, .item
              display: inline-block
              margin-right: 8px
              margin-bottom: 4px
              line-height: 16px
            .icon-thumb_up
              font-size: 12px
              color: rgb(0, 160, 220)
            .item
              padding: 0 6px
              border-radius: 1px
              font-size: 9px
              border: 1px solid rgba(7, 17, 27, 0.1)
          .rateTime
            position: absolute
            top: 0
            right: 0
</style>
