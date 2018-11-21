<template>
  <!--商品详情页，数据就是相关主键传过来的food数据-->
  <div>
    <transition name="move">
      <div v-show="showFlag" class="food" ref="myFood">
        <div class="food-content">
          <!--图片展示层-->
          <div class="image-header">
            <img :src="food.image" alt="">
            <!--用来定位图标-->
            <div class="back" @click="hide">
              <i class="icon-arrow_lift"></i>
            </div>
          </div>
          <!--内容层-->
          <div class="content">
            <h1 class="title">{{food.name}}</h1>
            <!--详情-->
            <div class="detail">
              <span class="sell-count">月售{{food.sellCount}}</span>
              <span class="rating">好评率{{food.rating}}%</span>
            </div>
            <!--价格-->
            <div class="price">
              <span class="now">￥{{food.price}}</span>
              <span v-show="food.oldPrice" class="old">￥{{food.oldPrice}}</span>
              <!--加入购物车绝对定位-->
            </div>
            <div class="cartcontrol-wrapper">
              <cartcontrol :food="food"></cartcontrol>
            </div>
            <!--没有该属性或者数量为0的时候显示加入购物车-->
            <div @click.stop.prevent="addFirst" class="buy" v-show="!food.count || food.count === 0 ">加入购物车</div>
          </div>
        </div>
        <!--分隔框-->
        <split v-show="food.info"></split>
        <!--商品介绍-->
        <div class="info" v-show="food.info">
          <!--有些数据不存在时不显示-->
          <h1 class="title">商家信息</h1>
          <p class="text">{{food.info}}</p>
        </div>
        <split></split>
        <div class="rating">
          <div class="title">商品评价</div>
          <ratingselect></ratingselect>
        </div>
      </div>
    </transition>
  </div>
</template>

<script>
  import BScroll from 'better-scroll'
  import cartcontrol from '../../components/cartcontrol/cartcontrol'
  import Vue from 'vue'
  import split from '../../components/split/split'
  import ratingselect from '../../components/ratingselect/ratingselect'

  export default {
    name: "food",
    props: {
      food: {
        type: Object
      }
    },
    data() {
      return {
        showFlag: false
      }
    },
    methods: {
      // 如果方法是组建私有的一般命名的时候加上下划线
      show() {
        this.showFlag = true;
        this.$nextTick(() => {
          if (!this.scroll) {
            this.scroll = new BScroll(this.$refs.myFood,{
              click: true
            });
          } else {
            this.scroll.refresh();
          }
        });
      },
      hide() {
        //关闭浮层
        this.showFlag = false;
      },
      addFirst(event){  //默认参数event
      console.log('click')
        if(!event._constructed){
          return;
        }
        Vue.set(this.food,'count',1)
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
    position:fixed
    left: 0
    top: 0
    bottom: 48px
    z-index: 30
    width: 100%
    background: white
    transition: all 0.2s linear
    &.move-enter-active, &.move-leave-active
      transform: translate3d(0, 0, 0)
    &.move-enter, &.move-leave-active
      transform: translate3d(100%, 0, 0)
    .image-header //不限制高度，图片根据高度显示全屏
      position: relative
      width: 100%
      height: 0
      padding-top: 100% //当padding值设置为100%后，相当于设置宽高相等的容器
      img
        position: absolute
        top: 0
        left: 0
        width: 100%
        height: 100%
      .back
        position: absolute
        top: 10px
        left: 0
        .icon-arrow_lift
          display: block
          padding: 18px //点击区域变大提高使用方便性
          font-size: 20px
          color: #fff
    .content
      position:relative
      padding: 18px
      .title
        line-height: 14px
        margin-bottom: 8px
        font-size: 14px
        font-weight: 700
        color: rgb(7, 17, 27)
      .detail
        margin-bottom: 18px
        line-height: 10px
        font-size: 0
        .sell-count, .rating
          font-size: 10px
          color: rgb(147, 153, 159)
        .sell-count
          margin-right: 12px
      .price
        font-weight: 700
        line-height: 24px
        .now
          margin-right: 8px
          font-size: 14px
          color: rgb(240, 20, 28)
        .old
          text-decoration: line-through
          font-size: 10px
          color: rgb(147, 153, 159)
      .cartcontrol-wrapper
        position: absolute
        right: 12px
        bottom: 12px
      .buy
        position: absolute
        right: 18px
        bottom: 18px
        z-index: 10
        height: 24px
        line-height: 24px
        padding: 0 12px
        box-sizing: border-box
        border-radius: 12px
        font-size: 10px
        color: #fff
        background: rgb(0, 160, 220)


    .info
      padding:18px
      .title
        line-height:14px
        margin-bottom:5px
        font-size:14px
        color:rgb(7,17,27)
      .text
        line-height:24px
        padding:0 8px
        font-size:12px
        color:rgb(77,85,93)

</style>
