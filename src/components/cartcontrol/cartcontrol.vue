<template>
<div class="cartcontrol">
<!--减按钮-->
<transition name="move">
  <div class="cart-decrease" v-show="food.count>0"
       @click.stop.prevent="decreaseCart"
  >
    <span class="icon-remove_circle_outline inner"></span>
  </div>
</transition>
  <div class="cart-count" v-show="food.count>0">{{food.count}}</div>
  <div class="cart-add icon-add_circle" @click.stop.prevent="addCart"></div>
</div>

</template>

<script>
import Vue from 'vue'
    export default {
        name: "cartcontrol",
        props:{
        // 关联的是单个的商品信息，传递过来单个的信息
          food:{
           type:Object
          }
        },
        methods:{
        // 添加event解决pc端的点击事件会触发两次的问题
          addCart(event){
          if(!event._constructed){
            return;
          }
          // console.log('click')
          // 判断是否存在数据，不存在点击后赋值为1
            if(!this.food.count){
            // 给foods添加一个count的属性和具体数据，解决不能检测到数据的变化的问题
              Vue.set(this.food,'count',1)
              this.food.count=1;
            }else{
              this.food.count++;
            }
            // 添加商品时通过一个事件获取对应的dom对象
            // this.$emit('cart-add', event.target);
          },

          decreaseCart(event){
            if(!event._constructed){
              return;
            }
            if(this.food.count){
              this.food.count--;
            }
          }
        }
    }
</script>

<style lang="stylus" rel="stylesheet/stylus">
  .cartcontrol
    font-size:0
    .cart-decrease
    //用户体验度的提高需要添加标签的padding
      display:inline-block
      padding:6px
      transition: all 0.4s linear
      .inner
        line-height: 24px
        font-size :24px
        color: rgb(0,160,220)
      &.move-enter-active,&.move-leave-active
        opacity:1
        transform:translate3d(0,0,0)
        .inner
          display:inline-block
          transform :rotate(0)
      &.move-enter, &.move-leave-active
        opacity: 0
        transform: translate3d(24px,0,0)
        .inner
          transform :rotate(180deg)
    .cart-count
      display:inline-block
      vertical-align :top
      height:12px
      padding-top:6px
      line-height:24px
      text-align:center
      font-size:10px
      color:rgb(147,153,29)
    .cart-add
      display: inline-block
      padding:6px
      line-height :24px
      font-size: 24px
      color :rgb(0,160,220)

</style>
