<template>
	<view class="qiun-columns">
		<view class="qiun-charts qiun-rows" >
			<canvas canvas-id="canvasPie" id="canvasPie" class="charts-pie" @touchstart="touchPie"></canvas>
		</view>
	</view>
</template>

<script>
	import uCharts from './u-charts/u-charts.js';
	var _self;
	var canvaPie=null;
   
	export default {
		data() {
			return {
				cWidth:'',
				cHeight:'',
				pixelRatio:1,
				serverData:'',
                piearr:[]
			}
		},
		onLoad() {
			_self = this;
			this.cWidth=uni.upx2px(750);
            this.cHeight=uni.upx2px(500);
            console.log(77777777)
			this.getServerData();
		},
		methods: {
			getServerData(){
                console.log(800000)
				uni.request({
					url: 'https://www.ucharts.cn/data.json',
					data:{
					},
					success: function(res) {
						console.log(res.data.data)
						let Pie={series:[]};
						//这里我后台返回的是数组，所以用等于，如果您后台返回的是单条数据，需要push进去
						Pie.series=res.data.data.Pie.series;
						_self.showPie("canvasPie",Pie);
					},
					fail: () => {
						_self.tips="网络错误，小程序端请检查合法域名";
					},
				});
			},
			showPie(canvasId,chartData){
				canvaPie=new uCharts({
					$this:_self,
					canvasId: canvasId,
					type: 'pie',
					fontSize:11,
					legend:{
					  show:true,
					  position:'right',
					  float:'center',
					  itemGap:10,
					  padding:5,
					  lineHeight:26,
					  margin:5,
					  borderWidth :1
					},
					background:'#FFFFFF',
					pixelRatio:_self.pixelRatio,
					series: chartData.series,
					animation: true,
					width: _self.cWidth*_self.pixelRatio,
					height: _self.cHeight*_self.pixelRatio,
					dataLabel: true,
					extra: {
						pie: {
						  labelWidth:15
						}
					},
				});
				this.piearr=canvaPie.opts.series;
			},
			touchPie(e){
				canvaPie.showToolTip(e, {
					format: function (item) {
						return item.name + ':' + item.data 
					}
				});
			},
		}
	}
</script>

<style>
.qiun-padding{padding:2%; width:96%;}
.qiun-wrap{display:flex; flex-wrap:wrap;}
.qiun-rows{display:flex; flex-direction:row !important;}
.qiun-columns{display:flex; flex-direction:column !important;}
.qiun-charts{width: 750upx; height:500upx;background-color: #FFFFFF;}
.charts-pie{width: 750upx; height:500upx;background-color: #FFFFFF;}
.charts-right{display:flex;align-items:center;width: 250upx; height:500upx;background-color: #FFFFFF;}
.legend-itme{width: 200upx; margin-left: 30upx; height:50upx;align-items:center;}
.legend-itme-point{width: 20upx; height:20upx; margin: 15upx;  border: 1px solid #FFFFFF; border-radius: 20upx;background-color: #000000;}
.legend-itme-text{height:50upx;line-height: 50upx;color: #666666;font-size: 26upx;}
</style>
