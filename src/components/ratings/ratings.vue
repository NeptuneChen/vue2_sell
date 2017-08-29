<template>
    <div class="ratings" ref="ratings">
        <div>
            <div class="ratings-content">
                <div class="overview">
                    <div class="overview-left">
                        <h1 class="score">{{seller.score}}</h1>
                        <div class="title">综合评分</div>
                        <div class="rank">高于周边商家{{seller.rankRate}}%</div>
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
                        <div class="delivery-wrapper">
                            <span class="title">送达时间</span>
                            <span class="delivery">{{seller.deliveryTime}}分钟</span>
                        </div>
                    </div>
                </div>
            </div>
            <split></split>
            <ratingselect :select-type="selectType" :only-content="onlyContent" :ratings="ratings" :desc="desc"></ratingselect>
            <div class="rating-wrapper border-1px">
                <ul>
                    <li v-for="rating in ratings" class="rating-item" v-show="needShow(rating.rateType, rating.text)">
                        <div class="avatar">
                            <img :src="rating.avatar" alt="" width="28" height="28">
                        </div>
                        <div class="content">
                            <h1 class="name">{{rating.username}}</h1>
                            <div class="star-wrapper">
                                <star :size="24" :score="rating.score"></star>
                                <span class="delivery" v-show="rating.deliveryTime">
                                {{rating.deliveryTime}}
                            </span>
                            </div>
                            <p class="text">{{rating.text}}</p>
                            <div class="recommend" v-show="rating.recommend && rating.recommend.length">
                                <i class="icon-thumb_up"></i>
                                <span class="item" v-for="item in rating.recommend">{{item}}</span>
                            </div>
                            <div class="time">
                                {{rating.rateTime | formatDate}}
                            </div>
                        </div>
                    </li>
                </ul>
            </div>
        </div>
    </div>
</template>
<script>
import BScroll from 'better-scroll';
import star from 'components/star/star';
import split from 'components/split/split';
import ratingselect from 'components/ratingselect/ratingselect';
import { formatDate } from 'common/js/date';
import data from 'common/json/data.json';

const ALL = 2;
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
            onlyContent: true,
            desc: {
                all: '全部',
                positive: '推荐',
                negative: '不满意'
            }
        };
    },
    created() {
        this.ratings = data.ratings;
        this.$nextTick(() => {
            // console.log(this.$refs.ratings);
            this.scroll = new BScroll(this.$refs.ratings, {
                click: true
            });
        });
        this.$root.eventHub.$on('ratingtype.select', (data) => {
            this.selectType = data;
            this.$nextTick(() => {
                this.scroll.refresh();
            });
        });
        this.$root.eventHub.$on('content.toggle', (data) => {
            this.onlyContent = !data;
            this.$nextTick(() => {
                this.scroll.refresh();
            });
        });
    },
    methods: {
        needShow(type, text) {
            if (this.onlyContent && !text) {
                return false;
            }
            if (this.selectType === ALL) {
                return true;
            } else {
                return type === this.selectType;
            }
        }
    },
    filters: {
        formatDate(time) {
            let date = new Date(time);
            return formatDate(date, 'yyyy-MM-dd hh:mm');
        }
    },
    components: {
        star,
        split,
        ratingselect
    }
};

</script>
<style lang="stylus" rel="stylesheet/stylus">
@import "ratings.styl";

</style>
