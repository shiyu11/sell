<template>
  <div class="star" :class="starType">
    <span v-for="itemClass in itemClasses" :class="itemClass"
          class="star-item"></span>
  </div>
</template>

<script>
  const LENGTH = 5
  const CLS_ON = 'on'
  const CLS_HALF = 'half'
  const CLS_OFF = 'off'

  export default {
    name: "star",
    // 分数和尺寸决定图片
    props: {
      size: {
        type: Number
      },
      score: {
        type: Number
      }
    },
    computed: {
      starType() {
        return 'star-' + this.size
      },
      itemClasses() {
        let result = [];
        // 向下取0.5倍的数值
        let score = Math.floor(this.score * 2) / 2
        // 变量控制小数
        let hasDecimal = score % 1 !== 0
        // 全星的数量，整数部分
        let integer=Math.floor(score)
        for(let i=0;i<integer;i++){
          result.push(CLS_ON);
        }
        if(hasDecimal){
          result.push(CLS_HALF);
        }
        while(result.length<LENGTH){
          result.push(CLS_OFF);
        }
        return result;
      }
    }
  }
</script>

<style lang="stylus" rel="stylesheet/stylus">
  @import "../../common/stylus/mixin.styl"
  .star
    .star-item
      display:inline-block
      background-repeat: no-repeat
    &.star-48
      .star-item
        width: 20px
        height: 20px
        margin-right: 22px
        background-size: 20px 20px
        &:last-child
          margin-right: 0
        /*上面itemClass的状态决定了图片*/
        &.on
          bg-image('star48_on')
        &.half
          bg-image('star48_half')
        &.off
          bg-image('star48_off')
    &.satr-36
      .star-item
        width: 15px
        height: 15px
        margin-right: 6px
        background-size: 15px 15px
        &:last-child
          margin-right: 0
        /*上面itemClass的状态决定了图片*/
        &.on
          bg-image('star36_on')
        &.half
          bg-image('star36_half')
        &.off
          bg-image('star36_off')
    &.satr-24
      .star-item
        width: 10px
        height: 10px
        margin-right: 3px
        background-size: 10px 10px
        &:last-child
          margin-right: 0
        /*上面itemClass的状态决定了图片*/
        &.on
          bg-image('star24_on')
        &.half
          bg-image('star24_half')
        &.off
          bg-image('star24_off')

</style>
