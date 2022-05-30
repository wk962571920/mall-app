<template>
	<view class="qiun-columns">
		<view class="qiun-charts qiun-rows" >
			<canvas canvas-id="canvasPie" id="canvasPie" class="charts-pie" @touchstart="touchPie"></canvas>
		</view>
	</view>
</template>

<script>
	import uCharts from '@/components/u-charts/u-charts.js';
	var _self;
	var canvaPie=null;
   
	export default {
		data() {
			return {
				cWidth:'',
				cHeight:'',
				pixelRatio:1,
                piearr:[],
                series: [{
                    name: "一班",
                    data: 50
                }, {
                    name: "二班",
                    data: 30
                }, {
                    name: "三班",
                    data: 20
                }, {
                    name: "四班",
                    data: 18
                }, {
                    name: "五班",
                    data: 8
                }]
			}
		},
		onLoad() {
			_self = this;
			this.cWidth=uni.upx2px(600);
            this.cHeight=uni.upx2px(400);
            console.log(77777777)
			this.getServerData();
		},
		methods: {
			getServerData(){
                // console.log(800000)
				// uni.request({
				// 	url: 'https://www.ucharts.cn/data.json',
				// 	data:{
				// 	},
				// 	success: function(res) {
				// 		console.log(res.data.data)
						let Pie={series:[]};
						//这里我后台返回的是数组，所以用等于，如果您后台返回的是单条数据，需要push进去
                        // Pie.series=res.data.data.Pie.series;
                        Pie.series = this.series
						_self.showPie("canvasPie",Pie);
				// 	},
				// 	fail: () => {
				// 		_self.tips="网络错误，小程序端请检查合法域名";
				// 	},
				// });
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
                    colors:['#FFA506','#90B956','#FFA506','#FF5824'],
					background:'#FFFFFF',
					pixelRatio:_self.pixelRatio,
					series: chartData.series,
					animation: true,
					width: _self.cWidth*_self.pixelRatio,
					height: _self.cHeight*_self.pixelRatio,
					dataLabel: true,
					extra: {
						pie: {
                          labelWidth:15,
                          border:true,
                          borderWidth:2
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

<style scoped>
.qiun-padding{padding:2%; width:96%;}
.qiun-wrap{display:flex; flex-wrap:wrap;}
.qiun-rows{display:flex; flex-direction:row !important;}
.qiun-columns{display:flex; flex-direction:column !important;}
.qiun-charts{width: 600upx; height:400upx;background-color: #FFFFFF;}
.charts-pie{width: 600upx; height:400upx;background-color: #FFFFFF;}
.charts-right{display:flex;align-items:center;width: 250upx; height:500upx;background-color: #FFFFFF;}
</style>
