<template>
    <div>
        <v-header :seller="seller"></v-header>
        <div class="tab">
            <div class="tab-item">
                <router-link to="/goods">商品</router-link>
            </div>
            <div class="tab-item">
                <router-link to="/ratings">评价</router-link>
            </div>
            <div class="tab-item">
                <router-link to="/seller">商家</router-link>
            </div>
        </div>
        <keep-alive><router-view :seller="seller"></router-view></keep-alive>
    </div>
</template>
<script>
import { urlParse } from 'common/js/util';
import header from 'components/header/header.vue';
import data from 'common/json/data.json';

export default {
    data() {
        return {
            seller: {
                id: (() => {
                    let queryPram = urlParse();
                    return queryPram.id;
                })()
            }
        };
    },
    created() {
        this.seller = data.seller;
    },
    components: {
        'v-header': header
    }
};

</script>
<style lang="stylus" rel="stylesheet/stylus">
@import "./common/stylus/mixin.styl";
.tab
    display:flex
    width:100%
    height:40px
    line-height:40px
    //border-bottom 1px solid rgba(7,17,27,0.1)
    border-1px(rgba(7,17,27,0.1))
    .tab-item
        flex:1
        text-align:center
        & > a
            display:block
            font-size:14px
            color:rgb(77,85,93)
            &.active
                 color:rgb(240,20,20)
</style>
