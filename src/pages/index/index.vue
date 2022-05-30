<!--
 * @Author: your name
 * @Date: 2020-09-01 10:12:26
 * @LastEditTime: 2021-01-18 15:14:13
 * @LastEditors: Please set LastEditors
 * @Description: In User Settings Edit
 * @FilePath: /miniapp-bodyBuilding/miniapp/src/pages/index/index.vue
-->
<template>
	<view class="home">
		<view class="header cu-chat align-center">
			<view class="title">决战卡路里</view>
			<image class="bg" src="/static/image1.png" mode="scaleToFill"></image>
			<view class="cu-bar search" style="width:600upx;margin-top:280upx;z-index:1;">
				<view class="search-form round">
					<text class="cuIcon-search search-icon"></text>
					<input class="search-input" @change="changeKey" @input="changeKey" v-model="key" :adjust-position="false" type="text" placeholder="请输入想要查询的食物名称，如香蕉" confirm-type="search">
				</view>
			</view>
		</view>
		<view class="content">
			<view class="hot-rank"> 
				<view class="h-title">热门排行</view>
				<view class="tags flex justify-between">
					<view class="item" @click="go(1)">高热食物</view>
					<view class="item1" @click="go(2)">高GI食物</view>
					<view class="item2" @click="go(3)">高GL食物</view>
				</view>
			</view>
			<view class="list">
				<view class="h-title">常见食物</view>
				<view class="card">
					<block v-for="item in dataSource" :key="item.id">
						<food :item="item" type="H" @gotoDetail="gotoDetail(item)"></food>
					</block>
				</view>
			</view>
		</view>
	</view>
</template>

<script>
import food from '@/components/food'
	export default {
		data() {
			return {
				title: 'Hello',
				list:[],
				key:''
			}
		},
		computed: {
			dataSource() {
				return this.list.filter(item => {
					if (this.key && !item.name.includes(this.key)) {return false}
					return true
				})
			}
		},
		async onLoad() {
			wx.showLoading({
				title:'加载中...',
				mask:true
			});
			wx.showShareMenu({
				withShareTicket:true,
				menus:['shareAppMessage','shareTimeline']
			})
			const isData = this.isStorege()
			if (isData) {
				this.list = JSON.parse(uni.getStorageSync('commonDatas'))
			} else {
				const data = await this.init()
				this.list = data.data
				uni.setStorageSync('commonDatas', JSON.stringify(this.list));
			}
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
			setTimeout(function () {
				wx.hideLoading();
			}, 1500);
		},
		components: {
			food
		},
		methods: {
			changeKey() {},
			go(w) {
				let url = ''
				if (w === 1) url = 'highHeat'
				if (w === 2) url = 'highGI'
				if (w === 3) url = 'highGL'
				uni.navigateTo({
					url: url
				});
			},
			gotoDetail(item) {
				uni.navigateTo({ url:"/pages/index/foodDetail?id="+item.id})
			},
			isStorege() {
				const value = uni.getStorageSync('commonDatas')
				if (value.length) return true
				return false
			},
			async init() {
				// 初始化数据库
				const db = wx.cloud.database({
					env:'test-env-bcs1s'
				})
				// 获取对应的集合
				const cs = db.collection('commonfoods')
				const MAX_LIMIT = 20
				// 先取出集合记录总数
				const countResult = await cs.count()
				const total = countResult.total
				// 计算需分几次取
				const batchTimes = Math.ceil(total / 20)
				// 承载所有读操作的 promise 的数组
				const tasks = []
				for (let i = 0; i < batchTimes; i++) {
					const promise = cs.skip(i * MAX_LIMIT).limit(MAX_LIMIT).get()
					tasks.push(promise)
				}
				// 等待所有
				return (await Promise.all(tasks)).reduce((acc, cur) => {
					return {
						data: acc.data.concat(cur.data),
						errMsg: acc.errMsg,
					}
				})

			}
		}
	}
</script>

<style>
	.header {
		width: 100%;
		height: 500upx;
		position: relative;
	}

	.bg {
		position: absolute;
		top: 0;
		left: 0;
		width: 100%;
		height: 500upx;
		z-index: 0;
	}
	.title {
		font-size: 36rpx;
		color: #222;
		margin-top: 55upx;
		font-weight: 400;
		z-index: 1;
	}
	
	.hot-rank{
		margin-top: 20upx;
		padding: 0 30upx;
	}

	.h-title {
		font-size: 28upx;
		color: #222;
		margin-bottom: 18upx;
	}

	.item,
	.item1,
	.item2 {
		color: white;
		padding: 30upx 40upx;
		border-radius: 26upx;
	}

	.item {
		background: linear-gradient(135deg, #FFBCC3 0%, #F076A7 100%);
	}

	.item1 {
		background: linear-gradient(135deg, #FFC8A9 0%, #EC7C44 100%);
	}

	.item2 {
		background: linear-gradient(135deg, #C2D35E 0%, #38C273 100%);
	}

	.list {
		margin-top: 20upx;
		padding: 0 30upx;
	}
</style>
