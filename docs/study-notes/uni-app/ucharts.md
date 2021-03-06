# 初试uCharts

> 相比于eCharts，[uCharts](http://doc.ucharts.cn/1073940)虽然没有那么大的生态圈，但毕竟是专门为uni-app开发的插件，其效果和功能或许更为合适

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
    var canvaColumn = null;
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
				canvaColumn = new uCharts({
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

## 几个必填项（required）

- `canvasId`

  用于填写图表的id，比如这里：

  ```html
  		<canvas canvas-id="canvasColumn" id="canvasColumn" class="charts"></canvas>
  <!-- id就是canvasColumn -->
  ```

- `$this`

  填入this的vue示例

- `type`

  这是啥图

- `categories`

  数据类别

- `series`

  数据

### 还有几个容易报错的配置

- `_self`

  因为**`$this`指向问题，官方推荐var一个`_self`来表示vue示例对象**（*然后在`onLoad`中赋值即可*）

  eg：

  ```js
  import uCharts from '../../components/u-charts/u-charts.js';
  var _self;
  onLoad() {
  	_self = this;
      this.getServerData();
  }
  ```

- `canvaColumn`

  这里要是没有var一个的话，**很有可能会出现类似`... is not defined`这样的报错**（*当然不一定是Column，还有可能是其他类型的图*）

  eg：

  ```js
  var canvaColumn=null;
  ...
  showColumn(canvasId,chartData){
  	canvaColumn=new uCharts({
        	...
      });
  ```

### 看个高级的示例：

```js
showColumn(canvasId,chartData){
    canvasColumn = new uCharts({
        $this: _self,		// 必选值: 传入this实例
        canvasId: canvasId,		// 这里是图表的id
        type: 'area',		// 采用区域图，link：http://doc.ucharts.cn/1172251
        legend: {		// 图例的各种设置（就是图下方的说明文字）
            show: true,		// 可单独用legend:true,效果与单独使用这条代码相同						
        },
        colors: ['#FF6600', '#3360DB', '#00FFCC'],	// 图标配色方案
        fontSize:11,		// 字体大小
        background:'#FFFFFF',	
        pixelRatio:_self.pixelRatio,	// 像素比，默认为1
        animation: true,	// 开启开场动画
        dataPointShape:false,	// 数据点的样式

        categories: chartData.categories,	// 数据类别，实际上算是数据的对应名称分类
        series: chartData.series,	// 数据列表
        xAxis: {
            disableGrid: true,	// 关闭x轴网格
            boundaryGap: 'center',
            axisLine: false		// 坐标轴轴线是否显示
        },
        yAxis: {
            disabled: true,		// 直接关闭Y轴
        },
        dataLabel: false,	// 是否在图表中显示数据内容值
        width: _self.cWidth * _self.pixelRatio,
        height: _self.cHeight * _self.pixelRatio,
        extra: {
            area: {
                type: "curve",
                opacity: 1,		//	区域图透明度
                gradient: true		// 开启区域图渐变色
            }
        }
    });
}
```

