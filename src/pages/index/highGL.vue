<!--
 * @Author: your name
 * @Date: 2020-09-03 10:55:18
 * @LastEditTime: 2020-09-09 15:09:30
 * @LastEditors: Please set LastEditors
 * @Description: In User Settings Edit
 * @FilePath: /miniapp-bodyBuilding/miniapp/src/pages/index/highGI.vue
-->
<template>
	<view class="hh">
		<view class="header cu-chat align-center">
			<view class="title">高GL食物</view>
            <view class="back cuIcon-back" @click="back"></view>
			<image class="bg" src="/static/highGL.png"></image>
            <view class="info">
                <view class="t">高GL食物</view>
                <view class="desc">GL是升糖负荷（Glycemic Load），反应吃不同质量的该食物，对血糖的影响程度。高GL的食物会让胰岛素分泌的时间延长，摄入低GL食物，会使胰岛素的分泌时间变短，减少脂肪的生成和堆积，血糖也较为平稳，食欲自然就不会那么强。</view>
            </view>
		</view>
        <view class="content">
            <block v-for="(item,index) in list" :key="item.id">
                <view class="m">第{{index+1}}名</view>
                <food :item="item" type="L" @gotoDetail="gotoDetail(item)"></food>
            </block>
        </view>
	</view>
</template>

<script>
import food from '@/components/food'
export default {
    data () {
        return {
            list:[]
        }
    },
    onLoad() {
         wx.showLoading({
            title:'加载中...',
            mask:true
        });
        wx.showShareMenu({
            withShareTicket:true,
            menus:['shareAppMessage','shareTimeline']
        })
        this.list = JSON.parse(uni.getStorageSync('commonDatas'))
        this.list = this.list.map(item=> {
            return {
                ...item,
                hotNUmber:parseInt(item.detail[0].nutrient[0][1]),
                EE:Number(item.detail[0].nutrient[1][1]).toFixed(1),
                CHO:Number(item.detail[0].nutrient[2][1]).toFixed(1),
                Pr:Number(item.detail[0].nutrient[3][1]).toFixed(1),
                unit:item.unit.split('可')[0] 
            }
        })
        this.list.sort((item1,item2) => item2.GL - item1.GL)
        setTimeout(function () {
            wx.hideLoading();
        }, 1500);
    },
    methods: {
       back() {
           uni.navigateBack()
       },
       gotoDetail (item) {
           uni.navigateTo({ url:"/pages/index/foodDetail?id="+item.id})
       }
    },
    components: {
       food 
    }
}
</script>

<style scoped>
    .header {
        /* autoprefixer: ignore next */
		width: 100%;
		height: 550upx;
		position: relative;
    }
    
    .back {
        font-size: 48upx;
        color: black;
        position: absolute;
        top: 64upx;
        left: 35upx;
        z-index:1;
    }

	.bg {
		position: absolute;
		top: 0;
		left: 0;
		width: 100%;
		height: 550upx;
		z-index: 0;
	}
	.title {
		font-size: 36rpx;
		color: #222;
        margin-top: 65upx;
        font-weight: 700;
		z-index: 1;
    }
    
    .info {
        position: absolute;
        width: 100%;
        padding: 0 20upx;
        bottom: 100upx;
        left: 0;
        color: white;
    }

    .t {
        font-size: 32upx;
    }

    .desc {
        font-size: 24upx;
    }

    .content {
        position: relative;
        background-color: white;
        margin-top: -55upx;
        padding: 44upx 30upx 20upx 30upx;
        border-radius: 60upx 60upx 0px 0px;
        z-index: 2;
    }

    .m {
        color: black;
        font-size: 28upx;
        margin-bottom: 18upx;
    }
</style>
