<!--
 * @Author: your name
 * @Date: 2020-09-07 17:12:46
 * @LastEditTime: 2020-09-09 15:08:53
 * @LastEditors: Please set LastEditors
 * @Description: In User Settings Edit
 * @FilePath: /miniapp-bodyBuilding/miniapp/src/compoents/food.vue
-->
<template>
    <view class="food flex" @click="gotoDetail">
        <image class="pic" :src="item.imgurl" mode="aspectFill"></image>
        <view class="f-r" style="flex:1">
            <view class="f-t flex justify-between align-center">
                <view class="food-name">{{item.name}}</view>
                <view class="flex align-center">
                    <image class="hot" src="/static/hot.png"></image>
                    <view class="food-hot" v-if="type === 'H'">{{item.hotNUmber}}大卡/{{item.unit}}</view>
					<view class="food-hot" v-if="type === 'L'">GL:{{item.GL}}</view>
					<view class="food-hot" v-if="type === 'I'">GI:{{item.GI}}</view>
                </view>
            </view>
            <view class="flex align-center">
                <view class="food-yy">蛋白质:{{item.Pr}}g</view>
                <view class="food-yy">脂肪:{{item.EE}}g</view>
                <view class="food-yy">碳水化合物:{{item.CHO}}g</view>
            </view>
        </view>
    </view>
</template>

<script>
export default {
	props: ['item','type'],
	methods: {
		gotoDetail() {
			this.$emit('gotoDetail')
		}
	}
}
</script>

<style scoped>
.food {
		border-radius: 30upx;
		border: 1px solid #E1E1E1;
		margin-bottom: 26upx;
		padding: 28upx 28upx;
	}

	.hot {
		width: 28upx;
		height: 28upx;
	}

	.pic {
		width: 120upx;
		height: 100upx;
		margin-right: 15px;
	}

	.food-name {
		width: 200upx;
		color: #000;
		font-size: 30upx;
		overflow: hidden;
		word-break: keep-all;
		word-wrap: break-word;
		white-space: nowrap;
		overflow: hidden;
		text-overflow: ellipsis;
	}

	.food-yy {
		font-size: 24upx;
		color: #000;
		margin-right: 12upx;
	}


	.f-r {
		display: flex;
		flex-direction: column;
		justify-content: space-between;
	}

	.food-hot {
		color: #FF5824;
		font-size: 25upx;
		padding-left: 10upx;
	}
</style>
