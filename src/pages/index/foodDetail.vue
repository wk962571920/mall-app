<template>
    <view class="detail" v-if="food">
        <view class="back cuIcon-back" @click="back"></view>
        <view class="header cu-chat align-center">
			<view class="title">{{food.formalName}}</view>
            <view class="back cuIcon-back" @click="back"></view>
		</view>
        <view class="content">
            <view class="food-name">{{food.formalName}}</view>
            <block v-if="food && food.otherName">
                <view class="food-othername" v-if="food.otherName !== '无'">别名:{{food.otherName}}</view>
            </block>
            <view class="flex align-center">
                <image class="hot" src="/static/hot.png"></image>
                <view class="flex align-center">
                    <view class="food-hot" v-if="food && food.hotNUmber && food.unit">{{food.hotNUmber}}大卡/{{food.unit}}</view>
                    <view class="food-hot" style="margin-left:20upx" v-if="food && food.GI">GI:{{food.GI}}</view>
                    <view class="food-hot" v-if="food && food.GL">GL:{{food.GL}}</view>
                </view>
            </view>
            <view class="nut flex justify-between">
                <view class="w" v-for="item in items" :key="item.title">
                    <view class="z" :style="item.color">{{item.number}}{{item.unit}}</view>
                    <view class="t">{{item.title}}</view>
                </view>
            </view>
            <view class="tips">{{food.tips}}</view>
            <view class="flex justify-between align-center" style="margin-top:30upx;">
                <view style="color:black;font-size:34upx;z-index:100">减肥人群</view>
                <view :style="textStyle" style="font-size:26upx;z-index:100">{{food.apply}}</view>
            </view>
            <view style="margin-top:30upx;" class="h-content">
                <view class="qiun-columns">
                    <view style="z-index:100" class="h-title">热量分解</view>
                    <view class="qiun-charts qiun-rows" >
                        <canvas canvas-id="canvasPie" id="canvasPie" class="charts-pie" @touchstart="touchPie"></canvas>
                    </view>
                </view>
            </view>
            <view class="kuang">
                <view class="k-title">矿物质</view>
                <view class="flex justify-between" style="margin-bottom:15upx" v-for="item in mineral" :key="item.title">
                    <view>{{item.title}}</view>
                    <view class="number">{{item.number}}</view>
                </view>
                <view class="more" @click="showMaoreMineral = !showMaoreMineral">{{textk}}</view>
            </view>
            <view class="kuang">
                <view class="k-title">维生素</view>
                <view class="flex justify-between" style="margin-bottom:15upx" v-for="item in vitamin" :key="item.title">
                    <view>{{item.title}}</view>
                    <view class="number">{{item.number}}</view>
                </view>
                <view class="more" @click="showVitamin = !showVitamin">{{textv}}</view>
            </view>
        </view>
    </view>
</template>

<script>
import pieCharts from '@/components/pieCharts'
import uCharts from '@/components/u-charts/u-charts.js';
var _self;
var canvaPie=null;
    
export default {
    data () {
        return {
            id:'',
            food:{},
            items:[],
            cWidth:'',
            cHeight:'',
            pixelRatio:1,
            piearr:[],
            showMaoreMineral:false,
            showVitamin:false
        }
    },
    onLoad(option) {
        _self = this;
        this.id=option.id;
        wx.showShareMenu({
            withShareTicket:true,
            menus:['shareAppMessage','shareTimeline']
        })
        this.cWidth=uni.upx2px(500);
        this.cHeight=uni.upx2px(350);
        this.init();
        this.getData()
    },
    components: {
      pieCharts  
    },
    computed: {
      textStyle () {
          if (this.food.apply) {
              if(this.food.apply.includes('推荐')) {
                  return 'color:#2EAC46'
              } else if (this.food.apply.includes('严禁')) {
                  return 'color:#eb3333'
              } else {
                  return 'color:#FFA506'
              }
          }
      },
      textk () {
          if (this.showMaoreMineral) return '收起'
            return '更多'
      },
      textv () {
          if (this.showVitamin) return '收起'
          return '更多'
      },
      mineral () {
          if (this.food.detail && this.food.detail.length) {
              if (this.showMaoreMineral) {
                  return this.food.detail[2].nutrient
              } else {
                  let arr = JSON.parse(JSON.stringify(this.food.detail[2].nutrient)).slice(0,2)
                  return arr
              }
          }
      },
      vitamin () {
          if (this.food.detail && this.food.detail.length) {
              if (this.showVitamin) {
                  return this.food.detail[1].nutrient
              } else {
                  let arr = JSON.parse(JSON.stringify(this.food.detail[1].nutrient)).slice(0,2)
                  return arr
              }
          }
      }
    },  
    methods: {
        back () {
            uni.navigateBack()
        },
        init () {
            uni.showLoading({
                mask:true,
                title:'加载中...'
            });
            let array = JSON.parse(uni.getStorageSync('commonDatas'))
            let obj = array.find(item=> item.id === this.id)
            if (obj) {
                this.food = obj
                this.food.hotNUmber=parseInt(obj.detail[0].nutrient[0][1]),
                this.food.EE = obj.detail[0].nutrient[1][1] === '一' ? 0 : Number(obj.detail[0].nutrient[1][1]).toFixed(1),
                this.food.CHO = obj.detail[0].nutrient[2][1] === '一' ? 0 : Number(obj.detail[0].nutrient[2][1]).toFixed(1),
                this.food.Pr = obj.detail[0].nutrient[3][1] === '一' ? 0 : Number(obj.detail[0].nutrient[3][1]).toFixed(1),
                this.food.CHOL = obj.detail[0].nutrient[4][1] === '一' ? 0 : Number(obj.detail[0].nutrient[4][1]).toFixed(1)
                this.food.unit = obj.unit.split('可')[0]
                this.food.formalName = obj.name.split(/\，|\,/)[0].split(/\（|\(/)[0]
                this.food.apply = obj.apply.split('：')[1]
                let arr = []
                for (let index = 0; index < obj.detail.length; index++) {
                    const item = obj.detail[index];
                    let a = []
                    for (let i = 0; i < item.nutrient.length; i++) {
                        const element = item.nutrient[i];
                        let obj = {
                            title:element[0],
                            number:element[1]
                        }
                        a.push(obj)
                    }
                    item.nutrient = a
                    arr.push(item)
                }
                console.log(777777,this.food)
                this.food.detail = arr
                 let pr = {
                    title:'蛋白质',
                    number:this.food.Pr,
                    hot:this.food.Pr * 4,
                    unit:'克',
                    color:'color:#50BCBC'
                }
                this.items.push(pr)
                let ee = {
                    title:'脂肪',
                    number:this.food.EE,
                    hot:this.food.EE * 9,
                    unit:'克',
                    color:'color:#90B956'
                }
                this.items.push(ee)
                let cho = {
                    title:'碳水化合物',
                    number:this.food.CHO,
                    hot:this.food.CHO * 4,
                    unit:'克',
                    color:'color:#FFA506'
                }
                this.items.push(cho)
                let d = {
                    title:'胆固醇',
                    number: this.food.CHOL,
                    hot:0,
                    unit:'毫克',
                    color:'color:#FF5824'
                }
                this.items.push(d)
            }
            setTimeout(function () {
				wx.hideLoading();
			}, 1500);
        },
        getData() {
                let array = JSON.parse(JSON.stringify(this.items))
                array.pop()
                array = array.map(item => {
                    let c = item.hot/this.food.hotNUmber
                    if (c === 0) c = 0
                    else c = Number((c * 100).toFixed(1))
                    let t = item.title
                    return {
                        name:t,
                        data:c
                    }
                })
                let Pie={series:[]};
                Pie.series = array
                _self.showPie("canvasPie",Pie); 
        },
        showPie(canvasId,chartData){
				canvaPie=new uCharts({
					$this:_self,
					canvasId: canvasId,
					type: 'pie',
                    fontSize:11,
                    colors:['#50BCBC','#90B956','#FFA506'],
                    legend:{show:true},
					pixelRatio:_self.pixelRatio,
					series: chartData.series,
					animation: true,
					width: _self.cWidth*_self.pixelRatio,
					height: _self.cHeight*_self.pixelRatio,
					dataLabel: true,
					extra: {
						pie: {
                          lableWidth:15,
                          border:true
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
			}
    }   
}
</script>

<style scoped>
    .detail {
        padding-bottom: 40upx;
    }
    .back {
        font-size: 48upx;
        color: black;
        position: absolute;
        top: 65upx;
        left: 35upx;
        z-index:1;
    }

	.title {
		font-size: 36rpx;
		color: #222;
		margin-top: 65upx;
        z-index: 1;
        padding-bottom: 8upx;
    }

    .content {
        padding: 0 35upx;
        margin-top: 40upx;
    }

    .hot {
		width: 29upx;
		height: 29upx;
    }
    .food-name {
        width: 200upx;
		color: #000;
		font-size: 32upx;
        font-weight: 700;
		overflow: hidden;
		word-break: keep-all;
		word-wrap: break-word;
		white-space: nowrap;
		overflow: hidden;
		text-overflow: ellipsis;
    }

    .food-othername {
        font-size: 25upx;
        color: #999;
        margin-bottom: 25upx;
    }

	.food-hot {
		color: #FF5824;
		font-size: 28upx;
		padding-left: 10upx;
    }

    .nut {
        margin-top: 30upx;
    }
    
    .w {
        background-color: #fff5f5;
        width: 150upx;
        height: 150upx;
        border-radius: 20upx;
        align-items: center;
        display: flex;
        flex-direction: column;
        justify-content: center;
    }

    .z {
        font-size: 30upx;
        margin-bottom: 8upx;
    }

    .t {
        font-size: 22upx;
        color: black;
    }

    .tips {
        color: #999;
        font-size: 28upx;
        line-height: 38upx;
        margin-top: 30upx;
    }

    .h-title {
        margin-bottom: -100upx;
        color: black;
        font-weight: 700;
        font-size: 30upx;
        padding-left: 25upx;
        padding-top: 25upx;
    }

    .h-content {
        border: 1upx solid #e1e1e1;
        border-radius: 25upx;
    }

.qiun-padding{padding:2%; width:96%;}
.qiun-wrap{display:flex; flex-wrap:wrap;}
.qiun-rows{display:flex; flex-direction:row !important;}
.qiun-columns{display:flex; flex-direction:column !important;
    border: 1upx solid #e1e1e;
}
.qiun-charts{
    height:400upx;
    width:100%;
}
.charts-pie{
    width: 500upx; 
    height:350upx;
    margin-left: 75upx;
    margin-top: 50upx;
}

.kuang {
    border: 1upx solid #e1e1e1;
    border-radius: 25upx; 
    padding: 25upx;
    margin-top: 30upx;
    color: black;
    font-size: 25upx;
}

.k-title {
    font-size: 30upx;
    color: black;
    font-weight: 700;
    padding-bottom: 20upx;
}

.number {
    font-size: 25upx;
    color: #999;
}

.more {
    width: 100%;
    font-size: 25upx;
    color: black;
    text-align: center;
}
</style>
