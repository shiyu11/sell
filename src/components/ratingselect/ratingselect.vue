<template>
  <div class="ratingselect">
    <div class="rating-type">
       <span>{{desc.all}}</span>
       <span>{{desc.positive}}</span>
       <span>{{desc.negative}}</span>
    </div>
    <div class="switch">
      <span class="icon-check_circle"></span>
      <span>只看有内容的评价</span>
    </div>

  </div>
</template>

<script>
  // const POSITIVE=0;
  // const NEGATIVE=1;
  const All=2;
    export default {
    // 先定义可以传入的变量
    props:{
      ratings:{
       type:Array,
       default(){
        return[];
       }
      },
      selcetType:{
        type:Number,
        default:All
      },
      onlyCotent:{
        type:Boolean,
        deault:false
      },
      desc:{
        type:Object,//样式函数
        default(){
          return{
            all:'全部',
            POSITIVE:'满意',
            NEGATIVE:'不满意'
          }
        }
      }
    }
    }
</script>
<style lang="stylus" rel="stylesheet/stylus">


</style>
