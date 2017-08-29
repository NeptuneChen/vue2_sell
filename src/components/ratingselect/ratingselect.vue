<template>
    <div class="ratingselect">
        <div class="rating-type border-1px">
            <span class="block positive" @click="select(2, $event)" :class="{'active': selectType === 2}">{{desc.all}}<span
        class="count">{{ratings.length}}</span></span>
            <span class="block positive" @click="select(0, $event)" :class="{'active': selectType === 0}">{{desc.positive}}<span
        class="count">{{positives.length}}</span></span>
            <span class="block negative" @click="select(1, $event)" :class="{'active': selectType === 1}">{{desc.negative}}<span
        class="count">{{nagatives.length}}</span></span>
        </div>
        <div class="switch" @click="toggleContent" :class="{'on':onlyContent}">
            <i class="icon-check_circle"></i>
            <span class="text">只看有内容的评价</span>
        </div>
    </div>
</template>
<script>
const POSITIVE = 0;
const NEGATIVE = 1;
const ALL = 2;
export default {
    props: {
        ratings: {
            type: Array,
            default () {
                return [];
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
        desc: {
            type: Object,
            default () {
                return {
                    all: '全部',
                    positive: '满意',
                    negative: '吐槽'
                };
            }
        }
    },
    computed: {
        positives() {
            return this.ratings.filter((rating) => {
                return rating.rateType === POSITIVE;
            });
        },
        nagatives() {
            return this.ratings.filter((rating) => {
                return rating.rateType === NEGATIVE;
            });
        }
    },
    methods: {
        select(type, event) {
            if (!event._constructed) {
                return;
            }
            this.$root.eventHub.$emit('ratingtype.select', type);
        },
        toggleContent(event) {
            if (!event._constructed) {
                return;
            }
            this.$root.eventHub.$emit('content.toggle', this.onlyContent);
        }
    }
};

</script>
<style lang="stylus" rel="stylesheet/stylus">
@import "ratingselect.styl";

</style>
