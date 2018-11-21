<template>
<!--主页-->
  <div>
    <!--左右两侧,内容没有滚动条超出部分是隐藏的-->
    <div class="goods">
      <!--左侧菜单-->
      <!--v-el:后面一定要用-连接不能驼峰式-->
      <div class="menu-wrapper" ref="menuWrapper">
        <ul>
          <!--当前遍历的循环值与设置的值相等时将样式添加上去 :class="{'current': currentIndex ===index}"-->
          <li v-for="(item,index) in goods" class="menu-item"
              :class="{'current': currentIndex ===index}"
              @click="selectMenu(index,$event)">  <!--传递一个事件-->
            <span class="text border-1px">
             <span v-show="item.type>0" class="icon"
                   :class="classMap[item.type]"></span>{{item.name}}
           </span>
          </li>
        </ul>

      </div>

      <!--右侧详情-->
      <div class="foods-wrapper" ref="foodsWrapper">
        <ul>
          <!--命名习惯通过js来选择的样式，没有实际效果的-->
          <li v-for="item in goods" class="food-list food-list-hook">
            <h1 class="title">{{item.name}}</h1>
            <ul>
                              <!--将事件也传递-->
              <li @click="selectFood(food,$event)"  v-for="food in item.foods" class="food-item border-1px">
                <!--商品具体内容分左右两块-->
                <div class="icon">
                  <img width="57" height="57" :src="food.icon" alt="">
                </div>
                <div class="content">
                  <h2 class="name">{{food.name}}</h2>
                  <p class="desc">{{food.description}}</p>
                  <div class="extra">
                    <span class="count">月销{{food.sellCount}}</span>
                    <span>好评率{{food.rating}}%</span>
                  </div>
                  <div class="price">
                    <span class="now">￥{{food.price}}</span><span v-show="food.oldPrice"
                                                                  class="old">￥{{food.oldPrice}}</span>

                  </div>
                  <!--价格平级的数量 v-on:cart-add="cartAdd"-->
                  <div class="cartcontrol-wrapper">
                    <cartcontrol :food="food"></cartcontrol>
                  </div>
                </div>
              </li>
            </ul>
          </li>
        </ul>

      </div>
      <!--购物车定位在视口底部-->
      <shopcart :delivery-price="seller.deliveryPrice"
                ref="shopcart"
                :min-price="seller.minPrice" :select-foods="selectFoods"></shopcart>
    </div>
    <food :food="selcetedFood" ref="food"></food>
  </div>
</template>

<script>
  import BScroll from 'better-scroll'
  import shopcart from '../../components/shopcart/shopcart'
  import cartcontrol from '../../components/cartcontrol/cartcontrol'
  import food from '../../components/food/food'

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
        selcetedFood:{}
      }
    },
    computed: {
      currentIndex() { //currentIndex对应菜单栏的下标
        for (let i = 0; i < this.listHeight.length; i++) { //不要忘了加this引用
          let height1 = this.listHeight[i];
          let height2 = this.listHeight[i + 1];
          //获得了一个区间的上下范围，判断scrollY落到这个区间，!height2是判断最后一个区间
          //避免i溢出，>= 向下的是一个闭区间，这样第一个就会高亮了
          // 判断滚动的高度大于当前小于变化后的高度，返回当前的数组索引，添加条件如果是最后一个和在区间内
          // 否则的返回0
          // 当数据遍历到最后一个height2为undefined所以需要添加条件
          if (!height2 || (this.scrollY >= height1 && this.scrollY < height2)) {
            return i; //映射到第5行menu的变化
          }
        }
        return 0;
      },
      selectFoods() {
        // 遍历goods
        let foods = []
        this.goods.forEach((good) => {
          good.foods.forEach((food) => {
            if (food.count) {
              foods.push(food);
            }
          })
        })
        return foods
      }
    },
    created() {
      this.$http.get('/api/goods').then((response) => {
        response = response.body;
        if (response.errno === ERR_OK) {
          this.goods = response.data;
          // dom发生真正的变化实在这个函数执行过后
          this.$nextTick(() => {
            // 保证dom已经渲染好
            this._initScroll();
            this._calculateHeight();
          })
        }
      });
      this.classMap = ['decrease', 'discount', 'special', 'invoice', 'guarantee'];
    },
    methods: {
      selectMenu(index, event) {

        // 保证pc端按钮被触发一次
        if (!event._constructed) {
          return;
        }
        let foodList = this.$refs.foodsWrapper.getElementsByClassName('food-list-hook');
        let el = foodList[index];
        // 点击跳转到对应的元素位置
        // （元素，时间）
        this.foodsScroll.scrollToElement(el, 300);
      },
      selectFood(food,event){
        if (!event._constructed) {
          return;
        }
        this.selcetedFood=food;
        // 父组件可以调用子组件的方法，子组件不能直接调用父组件的方法
        this.$refs.food.show();
      },
      _initScroll() {
        this.menuScroll = new BScroll(this.$refs.menuWrapper, {
          click: true
        })

        this.foodsScroll = new BScroll(this.$refs.foodsWrapper, {
          //添加true后面的点击事件才能奏效
          click: true,
          // 属性的传递probeType，希望scroll滚动时能监测位置
          probeType: 3
        });

        // foodsScroll对象添加一个事件，当滚动时能获取信息滚动时将数据转换为正整数
        this.foodsScroll.on('scroll', (pos) => {
          this.scrollY = Math.abs(Math.round(pos.y));
          // 获取滚动的数据后考虑如何和右侧的高度想映射，vue的计算属性
        })
      },
      _calculateHeight() {
        // 获取 右侧每一个循环的li的内容
        let foodList = this.$refs.foodsWrapper.querySelectorAll('.food-list-hook')
        // foodList是列数组遍历获取每个列数组
        // 累加每个列数组再添加到数组中去
        let height = 0;
        this.listHeight.push(height);
        for (let i = 0; i < foodList.length; i++) {
          // 获取每一个列的高度在累加
          let item = foodList[i];
          height += item.clientHeight;
          this.listHeight.push(height);
        }

      },
      // cartAdd (el) {
      //   // dom元素更新后执行， 因此此处能正确打印出更改之后的值；
      //   this.$nextTick(() => {
      //     // 调用shopcart组件的drop()函数
      //     this.$refs.shopcart.drop(el);
      //   });
      // }
    },
    components: {
      shopcart,
      cartcontrol,
      food
    },

  }
</script>

<style lang="stylus" rel="stylesheet/stylus">
  @import "../../common/stylus/mixin.styl"
  .goods
    display: flex
    position: absolute
    top: 174px
    bottom: 46px
    width: 100%
    overflow: hidden
    .menu-wrapper
      /*等分 缩放情况 占位*/
      flex: 0 0 80px
      width: 80px
      background: #f3f5f7
      .menu-item
        display: table ///*垂直居中的实现*/
        height: 54px
        width: 56px
        padding: 0 12px
        line-height: 14px
        &.current
          position: relative
          z-index: 10
          margin-top: -1px
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
          width: 56px
          //垂直居中
          vertical-align: middle
          border-1px(rgba(7, 17, 27, 0.1))
          font-size: 12px
    .foods-wrapper
      flex: 1
      .title
        padding-left: 14px
        height: 26px
        line-height: 26px
        border-left: 2px solid #d9dde1
        font-size: 12px
        color: rgb(147, 153, 159)
        background: #f3f5f7
      .food-item
        display: flex
        margin: 18px
        //上下的margin重叠起来需要padding
        padding-bottom: 18px
        border-1px(rgba(7, 17, 27, 0.1))
        &:last-child
          border-none()
          margin-botton: 0
        .icon
          flex: 0 0 57px
          margin-right: 10px
        .content
          flex: 1
          .name
            margin: 2px 0 8px
            height: 14px
            line-height: 14px
            font-size: 14px
            color: rgb(7, 17, 27)
          .desc, .extra
            line-height: 10px
            font-size: 10px
            color: rgb(147, 153, 159)
          .desc
            line-height: 12px
            margin-bottom: 8px
          .extra
            .count
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
            right: 0
            bottom: 12px
</style>
