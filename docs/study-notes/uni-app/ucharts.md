# 初试uCharts

直接上vue单页面代码（需提前导入[ucharts](https://ext.dcloud.net.cn/plugin?id=271)）：

```vue
<template>
	<view>
		<canvas canvas-id="canvasColumn" id="canvasColumn" class="charts"></canvas>
	</view>
</template>

<script>
	import uCharts from '@/components/u-charts/u-charts.js';
	var _self;
	export default {
		data() {
			return {
				cWidth:'',
				cHeight:'',
				pixelRatio:1,
			}
		},
		onLoad() {
			_self = this;
			this.cWidth=uni.upx2px(750);
			this.cHeight=uni.upx2px(500);
			let Column = {
				categories: ["2013","2014","2015","2016","2017","2018"],
				series:[
					{
						"name":"新成交量3",
						"data":[35,36,31,33,13,34],
					},{
						"name":"新成交量4",
						"data":[18,27,21,24,6,28],
					}]
			};
			this.showColumn("canvasColumn",Column);
		},
		methods: {
			showColumn(canvasId,chartData){
				canvasColumn = new uCharts({
					$this: _self,		// 必选值: 传入this实例
					canvasId: canvasId,		// 这里是图表的id
					type: 'column',		// 这里是类型
					legend: true,		// 
					fontSize:11,		// 字体大小
					background:'#FFFFFF',	
					pixelRatio:_self.pixelRatio,	// 像素比，默认为1
					animation: true,	// 开启开场动画
					categories: chartData.categories,	// 数据类别，实际上算是数据的对应名称分类
					series: chartData.series,	// 数据列表
					xAxis: {
						disableGrid:true,	// 关闭x轴网格
					},
					yAxis: {
					},
					dataLabel: true,	// 是否在图表中显示数据内容值
					width: _self.cWidth*_self.pixelRatio,
					height: _self.cHeight*_self.pixelRatio,
					extra: {
						column: {
						  width: _self.cWidth*_self.pixelRatio*0.45/chartData.categories.length		// 柱状图的柱子宽度
						}
					  }
				});
			},
		}
	}
</script>

<style>
	page{background:#F2F2F2;width: 750upx;overflow-x: hidden;}
	.charts{width: 750upx; height:500upx;background-color: #FFFFFF;}
</style>
```

页面效果：

![image-20210204151011991](https://gitee.com/rogerskelamen/mdpic/raw/master/img/image-20210204151011991.png)

## 配置项详解

`opts.type`（required）是图表类型，可选值为pie、line、column、area、ring、radar、arcbar、gauge、candle、bar、rose、word、mix（mix类型下的extra配置详细某一类图的配置未完善，需要自己改源码）
一般**可以通过`extra`给出更详细的一类图的配置，如`line`类型默认为straight，可改选为curve**

```js
extra: {
    line: {
        type: 'curve',
    }
}
```

