<template>
    <div>
        <div class="goods">
            <div class="menu-wrapper" ref="menuWrapper">
                <ul>
                    <li v-for="(item,index) in goods" class="menu-item" :class="{'current':currentIndex === index}" @click="selectMenu(index,$event)" ref="menuList">
                        <span class="text border-1px">
                    <span v-show="item.type>0" class="icon" :class="classMap[item.type]"></span>{{item.name}}
                        </span>
                    </li>
                </ul>
            </div>
            <div class="foods-wrapper" ref="foodsWrapper">
                <ul>
                    <li v-for="item in goods" class="food-list food-list-hook">
                        <h1 class="title">{{item.name}}</h1>
                        <ul>
                            <li @click="selectFood(food,$event)" v-for="food in item.foods" class="food-item border-1px">
                                <div class="icon">
                                    <img width="57" height="57" :src="food.icon">
                                </div>
                                <div class="content">
                                    <h2 class="name">{{food.name}}</h2>
                                    <p class="desc">{{food.description}}</p>
                                    <div class="extra">
                                        <span class="count">月售{{food.sellCount}}</span><span>好评率{{food.rating}}%</span>
                                    </div>
                                    <div class="price">
                                        <span class="now">¥{{food.price}}</span><span class="old" v-show="food.oldPrice">¥{{food.oldPrice}}</span>
                                    </div>
                                    <div class="cartcontrol-wrapper">
                                        <cartcontrol :food="food"></cartcontrol>
                                    </div>
                                </div>
                            </li>
                        </ul>
                    </li>
                </ul>
            </div>
            <shopcart ref="shopcart" :selectFoods="selectFoods" :delivery-price="seller.deliveryPrice" :min-price="seller.minPrice"></shopcart>
        </div>
        <food :food="selectedFood" ref="food"></food>
    </div>
</template>
<script>
import BScroll from 'better-scroll';
import shopcart from 'components/shopcart/shopcart';
import cartcontrol from 'components/cartcontrol/cartcontrol';
import food from 'components/food/food';
import data from 'common/json/data.json';

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
            drop: [],
            selectedFood: {}
        };
    },
    computed: {
        currentIndex() {
            for (let i = 0; i < this.listHeight.length; i++) {
                let height1 = this.listHeight[i];
                let height2 = this.listHeight[i + 1];
                if (!height2 || (this.scrollY >= height1 && this.scrollY < height2)) {
                    this._followScroll(i);
                    return i;
                }
            }
            return 0;
        },
        selectFoods() {
            let foods = [];
            this.goods.forEach((good) => {
                good.foods.forEach((food) => {
                    if (food.count) {
                        foods.push(food);
                    }
                });
            });
            return foods;
        }
    },
    created() {
        this.$root.eventHub.$on('cart.add', (data) => {
            this.event = data;
            this._drop(this.event);
        });
        this.classMap = ['decrease', 'discount', 'guarantee', 'invoice', 'special'];
        this.goods = data.goods;
        this.$nextTick(() => {
            // console.log(this);
            this._initScroll();
            this._calculateHeight();
        });
    },
    methods: {
        selectMenu(index, event) {
            // 把原生自带的click事件过滤掉
            if (!event._constructed) {
                return;
            }
            // console.log(index);
            let foodList = this.$refs.foodsWrapper.getElementsByClassName('food-list-hook');
            let el = foodList[index];
            this.foodScroll.scrollToElement(el, 300);
        },
        selectFood(food, event) {
            if (!event._constructed) {
                return;
            }
            this.selectedFood = food;
            this.$refs.food.show();
        },
        _drop(target) {
            // 体验优化，异步执行下落动画
            this.$nextTick(() => {
                this.$refs.shopcart.drop(target);
            });
        },
        _initScroll() {
            this.meunScroll = new BScroll(this.$refs.menuWrapper, {
                click: true
            });
            this.foodScroll = new BScroll(this.$refs.foodsWrapper, {
                click: true,
                probeType: 3
            });
            this.foodScroll.on('scroll', (pos) => {
                if (pos.y <= 0) {
                    this.scrollY = Math.abs(Math.round(pos.y));
                }
            });
        },
        _calculateHeight() {
            let foodList = this.$refs.foodsWrapper.getElementsByClassName('food-list-hook');
            let height = 0;
            this.listHeight.push(height);
            for (let i = 0; i < foodList.length; i++) {
                let item = foodList[i];
                height += item.clientHeight;
                this.listHeight.push(height);
            }
        },
        _followScroll(index) {
            let menuList = this.$refs.menuList;
            let el = menuList[index];
            this.meunScroll.scrollToElement(el, 300, 0, -100);
        }
    },
    components: {
        shopcart,
        cartcontrol,
        food
    }
};

</script>
<style lang="stylus" rel="stylesheet/stylus">
@import "goods.styl";

</style>
