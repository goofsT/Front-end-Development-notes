# Echarts基本配置

### 初始化

```js
//创建实例
let myEchats=echarts.init(document.getElementById("test"))
//设置配置项
myEchats.setOption(option)
//销毁实例
myEchats.dispose
//响应容器大小
 window.onresize = function() {//单个
    myChart.resize();
  };
 window.addEventListener(“resize”, function () {//多个图表设置自适应
          myChart.resize();
});

```



### title图表标题配置项

```js
setOption({
    //图表标题
	title:{
        text:"主标题"，//主标题
        link:"www.baidu.com",//主标题链接
        subLink:"www.baidu.com",//副标题链接
        target:"self",//当前页打开超链接，
        backgroundColor:"pink"//背景色
        borderColor:"red",//边框颜色
        borderWidth:5//边框宽度
        x:"center",//主标题水平居中
        subText:"副标题"//副标题,
        textStyle:{//文字样式
       		fontSize:30,
        	color:"yellow"
    	}
    }
})
```

### tooltip提示框配置

```js
setOption({
    //提示框
	tooltip:{
        trigger:"item",//触发类型,item:图形触发，axis：坐标轴触发
        zaisPointer:{//坐标轴指示器
            type:"line",//默认line实线 shadow阴影 cross十字准星
        },
        showContent:false,//不显示内容
        backgroundColor:"pink",
       	//提示框浮层内容格式,可以是字符串模板，也可以是回调函数
         //提示框内容样式
            /*字符模板{a},{b},{c},{d},{e}
            折线,柱状，K线：{a}（系列名称），{b}(类目值)，{c}(数值)
            散点图：{a}（系列名称），{b}(数据名称)，{c}(数值数组)
            地图：{a}（系列名称），{b}(区域名称)，{c}(合并数值)
            饼图：{a}（系列名称），{b}(数据项名称)，{c}(数值) {d}(百分比) 
            */
        formater:function(params){
            return params[0].xxx
        }
    }
})
```

### legend图例配置

```js
myChart.setOption({
legend: {
show: true, //是否显示
type: "plain",//图例类型，scroll:滚动翻页的图例
top: "55%", // 组件离容器的距离
bottom:"20%", // 组件离容器的距离
icon:"circle",//图形改为方形
left 的值可以是像 20 这样的具体像素值，可以是像 '20%' 这样相对于容器高宽的百分
比，也可以是 'left', 'center', 'right'
right: "5%",
padding: 5, // 图例内边距
itemWidth: 6, // 图例标记的图形宽度。
itemGap: 20, // 图例每项之间的间隔。
itemHeight: 14, // 图例标记的图形高度。
selectedMode: false, // 图例选择的模式，控制是否可以通过点击图例改变系列的显示状态。默认开启图例选择，可以设成 false 关闭。
inactiveColor: "#fffddd", // 图例关闭时的颜色。
textStyle: {//图例的公用文本样式。
 color: "#aabbcc", // 文字的颜色。
 fontStyle: "normal", // 文字字体的风格。'italic'
 fontWeight: "normal", // 文字字体的粗细。 'normal' 'bold' 'bolder'
'lighter' 100 | 200 | 300 | 400...
 itemStle:{//图例的图形样式
    color:"xxx",
}
}

```

### grid直角坐标系内绘网格

```js
myChart.setOption({
    gride:{
        show:true,//是否显示直角坐标系
        left:"5%",//距离容器的位置
        backgroundColor:"pink",
        borderColor:"red"
    }
})
```



​	

### 柱状图

```js
let xData=["美食","娱乐","音乐","游戏"]
let yData=[11,22,33,44]
let option = {
	xAxis: {//配置x轴坐标参数
	data: xData,
	type: "category", //坐标轴类型。'value' 数值轴，适用于连续数据。
	// 'category' 类目轴，适用于离散的类目数据。为该类型时类目数据可自动从 series.data或 dataset.source 中取，或者可通过 xAxis.data 设置类目数据。
	// 'time' 时间轴，适用于连续的时序数据，与数值轴相比时间轴带有时间的格式化，在刻度计算上也有所不同，例如会根据跨度的范围来决定使用月，星期，日还是小时范围的刻度。
	// 'log' 对数轴。适用于对数数据。
    },
    yAxis:{//配置y轴坐标轴参数
        type："value"//同x轴的参数
    }
    series:[//系列
    	{
    	tpe:"bar",//系列类别
    	name:"销量",//系列名称，用于提示框组建的显示，
    	data:yData,//数据
    	barWidth:20,//柱子宽度
    	markPoint:{//数据标注点
    		data:[
    			{type:"max",name:"最大值"},
    			{type:"min",name:"最小值"},
   			 ]
		},
         markLine:{//数据标注线
             data:[
                 {type:"average",name:"平均值"},
             ]
         }   
    	itemStyle:{//设置每个柱子颜色设置
    		color:function(params){
                 let colorList=["#c23531","#2f4554","#61a0a8","#d48265"]
                 reutrn colorList[params.dataIndex]
            	}
			}
		}
    ]
},
```

### 饼图

```js
let data=[{ value: 67, name: '美食' },
		{ value: 85, name: '日化' },
		{ value: 45, name: '数码' },
		{ value: 98, name: '家电' }]

let data=[{ value: 67, name: '美食',itemStyle:{color:"red"}},//为每一项设置不同颜色
		{ value: 85, name: '日化' itemStyle:{color:"pink"}},
		{ value: 45, name: '数码' itemStylel:{color:"white"}},
		{ value: 98, name: '家电' itemStyle:{color:"blue"}}]
let option={
    tooltip:{
        trigger:"item",//图形触发
    }
    series:[
        {
            name:"销量",
            type:"pie"，
            data,//数据
            radius: ['40%', '70%'],//饼图的半径。数组的第一项是内半径，第二项是外半径。
    		label:{//饼图图形上的文本标签
                show：true,
                position:"inside",//outside扇区外侧， inside:扇区内部,center:饼图中心位置			
                formatter:'{b}:{c}({d}%)'  //b:name,c:value,d:百分比  
            },
    		itemStyle:{//设置图形样式   
                shadowColor:"red"//阴影
                shadowBlur:200//阴影度
            }
        }
    ]
}
```

### 折线图

```js
let xData=["Mon", "Tue", "Wed", "Thu", "Fri", "Sat", "Sun"]
let option={
    xAxis:{
        type:"category",//类目轴
        data:xData,
    }
    yAxis:{
    	type:"value"
	},
    series:[
        {
            name:"美食",
    	    type:"line",
            smooth:true,//平滑线
            areaStyle:{//区域样式
                color:"red"
            },
            stack:"num",//数据堆叠同个类目轴上系列配置相同的stack值后，后一个系列的值会在前一个系列的值上相加。
            data:[1,2,5,3,5,7,8],
            emphasis:{
            	focus:"series",//聚焦当前高亮的数据所在的系列的所有图形
        	},
            dataZoom:[//数据区域缩放
                {type:"slider",xAxisIndex:0,filterMode:"none"},
                {type:"slider",yAxisIndex:0,filterMode:"none"}
            ]
        }，
         {
            name:"娱乐",
    	    type:"line",
            stack:"num",//数据堆叠同个类目轴上系列配置相同的stack值后，后一个系列的值会在前一个系列的值上相加。
            data:[5,2,5,8,9,7,10]j,
            emphasis:{
            	focus:"series",//聚焦当前高亮的数据所在的系列的所有图形
        	}
        }
    ]
}
```

### 树形图

```js
let myEcahrts=echarts.init(this.$refs.mychart)
let data={// 注意，最外层是一个对象，代表树的根节点
	name: "层级1",// 节点的名称，当前节点 label 对应的文本
	children: [
			{
				name: "层级2",
				children: [
						{
						name: "层级3-1",
						children: [//子节点
							 { name: "数据1", value: 3938 }, // value 值，只在 tooltip 中显示
							 { name: "数据2", value: 3812 },
							 { name: "数据3", value: 6714 },
							 { name: "数据4", value: 743 },
							],
						},						
						{
						name: "层级3-2",
						children: [
							{ name: "数据1", value: 3534 },
							{ name: "数据2", value: 5731 },
							{ name: "数据3", value: 7840 },
							{ name: "数据4", value: 5914 },
							{ name: "数据5", value: 3416 },
							],
						},
						],
};
        
let options={
  	tooltip:{
        trigger:"item"
    },
    series:[
        {
            type:"tree",//树图
            data:[data],
            symbolSize:15//标签大小
            orient:"LR",//排列方向，LR:从左到右 TB：从上到下
            top:"10%",//距离容器距离
            label:{//描述了每个节点所对应的文本标签的样式
            	rotate:90,//文本旋转角度
                position:"left",//标签的位置
                verticalAlign:"middle",//文字垂直对齐方式
                align:"right"，//文字水平对齐方式
                fontSize:9,//文字大小
            },
            leaves:{//叶子节点的特殊配置
                label:{//叶子节点对应的文本标签样式
                    position:"right"
                }
            },
            empasis:{//树图中各图形和标签的高亮样式
                focus:"descendant"//聚焦所有子孙节点
            },
            expandAndCollapse: true,//子树折叠和展开的交互由于绘图区域是有限的，而通常一个树图的节点可能会比较多，这样就会出现节点之间相互遮盖的问题。为了避免这一问题，可以将暂时无关的子树折叠收起，等到需要时再将其展开。如上面径向布局树图示例，节点中心用蓝色填充的就是折叠收起的子树，可以点击将其展开。
            animationDuration: 550,
		   animationDurationUpdate: 750,
        }
    ]
  }
```

### 事件

在 ECharts 中主要通过 [on](https://echarts.apache.org/zh/api.html#echartsInstance.on) 方法添加事件处理函数，该文档描述了所有 ECharts 的事件列表。

ECharts 中的事件分为两种，一种是鼠标事件，鼠标事件包括：'click'`、 `'dblclick'`、 `'mousedown'`、 `'mousemove'`、 `'mouseup'`、 `'mouseover'`、 `'mouseout'`、 `'globalout'`、 `'contextmenu' 在鼠标点击某个图形上会触发，还有一种是 调用 [dispatchAction](https://echarts.apache.org/zh/api.html#echartsInstance.dispatchAction) 后触发的事件。

> 示例

```js
//params是包含点击图形的数据信息的对象
myChart.on('click', function (params) {
    console.log(params);
});

myChart.on('legendselectchanged', function (params) {
    console.log(params);
});

chart.on('click', 'series.line', function (params) {
    console.log(params);
});

chart.on('mouseover', {seriesIndex: 1, name: 'xx'}, function (params) {
    console.log(params);
});
```

