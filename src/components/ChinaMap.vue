<template>
    <echart :options="options" ref="cmap" theme="darkyc" auto-resize></chart>
</template>

<script>
import echarts from './china.js'

const geoCoordMap = {
    '北京': [116.4551, 40.2539],
    '天津': [117.4219, 39.4189],
    '河北': [114.4995, 38.1006],
    '山西': [112.3352, 37.9413],
    '内蒙古': [111.4124, 40.4901],
    '辽宁': [123.1238, 42.1216],
    '吉林': [125.8154, 44.2584],
    '黑龙江': [127.9688, 45.368],
    '上海': [121.4648, 31.2891],
    '江苏': [118.8062, 31.9208],
    '浙江': [119.5313, 29.8773],
    '安徽': [117.29, 32.0581],
    '福建': [119.4543, 25.9222],
    '江西': [116.0046, 28.6633],
    '山东': [117.1582, 36.8701],
    '河南': [113.4668, 34.6234],
    '湖北': [114.3896, 30.6628],
    '湖南': [113.0823, 28.2568],
    '广东': [113.5107, 23.2196],
    '广西': [108.479, 23.1152],
    '海南': [110.3893, 19.8516],
    '重庆': [107.7539, 30.1904],
    '四川': [103.9526, 30.7617],
    '贵州': [106.6992, 26.7682],
    '云南': [102.9199, 25.4663],
    '西藏': [91.1865, 30.1465],
    '陕西': [109.1162, 34.2004],
    '甘肃': [103.5901, 36.3043],
    '青海': [101.4038, 36.8207],
    '银川': [106.3586, 38.1775],
    '新疆': [87.9236, 43.5883],
    '新疆兵团': [85.42, 41.82],
};

const colorYellow = '#FF9630' ;
const keyname = '来源地';

function defaultOptions() {
	let options = {
		title: {
			text: '',
			subtext: '',
			left: 'center',
			textStyle: {
				color: '#fff'
			},
			top: '20%',
			left: 'center',
		},
		tooltip: {
			trigger: 'item',
			formatter: function(params) {
				var res = params.seriesName + '<br/>';
				if (params.seriesType == 'lines') {
					res += params.data.fromName + ' > ' + params.data.toName;
					var myseries = options.series;
					for (var i = 0; i < myseries.length; i++) {
						if (myseries[i].name == params.seriesName &&
							myseries[i].type == 'effectScatter') {
							for (var j = 0; j < myseries[i].data.length; j++) {
								if (myseries[i].data[j].name == params.data.fromName) {
									res += '：' + myseries[i].data[j].value[2] + '人';
								}
							}
						}
					}
				} else if (params.seriesType == 'effectScatter') {
					res += params.data.name + '：' + params.data.value[2] + '人';
				}
				return res;
			},
		},
		geo: {
			map: 'china',
			label: {
				emphasis: {
					show: false
				}
			},
			roam: false,
			zoom: 1.2,
			itemStyle: {
				normal: {
					areaColor: '#323C48',
					borderColor: '#404A59'
				},
				emphasis: {
					areaColor: '#323C48'
				}
			}
		},
		series: [
			/* 0 fromTo 连线 */
			{
				name: keyname,
				type: 'lines',
				z: 1,
				label: {
					emphasis: {
						show: true,

					}
				},
				lineStyle: {
					normal: {
						color: colorYellow,
						width: 2,
						opacity: 0.8,
						curveness: 0.2 //曲线的弯度
					},
					emphasis: {
						color: colorYellow,
						width: 6,
						shadowColor: 'rgba(0, 0, 0, 0.5)', 
						shadowBlur: 10, 
					}
				},
				data: [], 
			}, 
			/* 1 流动动画 */
			{
				name: keyname,
				type: 'lines',
				zlevel: 2,
				z: 2,
				animation: false,
				effect: {
					show: true,
					constantSpeed: 50, // 移动像素/s
					trailLength: 0.4, // 轨迹长度0~1
					color: '#fff',
					symbolSize: 5
				},
				lineStyle: {
					normal: {
						color: colorYellow,
						width: 0,
						curveness: 0.2 // 同上
					}
				},
				data: [], 
			}, 
			// 2 地点缩放动画
			{
				name: keyname,
				type: 'effectScatter',
				coordinateSystem: 'geo',
				z: 3,
				rippleEffect: {
					brushType: 'stroke'
				},
				showEffectOn: 'render',
				symbolSize: function(val) {
					if ([val[0], val[1]].toString() == geoCoordMap[destination].toString()) {
						return 30;
					}
					return 15;
				},
				label: {
					normal: {
						show: true,
						position: 'right',
						formatter: '  {b}',
						textStyle: {
						  color: colorYellow,
						},
					}
				},
				itemStyle: {
					normal: {
						color: colorYellow
					}
				},
				data: [], 
			}, 
		]
	};
	return options;
}

var convertData = function(data) {
    var res = [];
    res = data.map(function(dataItem) {
        var res = {};
        var fromCoord = geoCoordMap[dataItem.name];
        var toCoord = geoCoordMap[destination];
        var b = fromCoord && toCoord;
        if (fromCoord && toCoord) {
            return {
                fromName: dataItem.name,
                toName: destination,
                coords: [fromCoord, toCoord]
            };
        }
        return res;
    })
    return res;
};

const key = 'value';
const destination = '银川';

export default {
  data() {
    // init 
	let options = defaultOptions();
	options.title.text = this.title;
	if(this.dimension.length){
	  this.initSeries(options, this.dimension)
	}
    return {
	  options: options
	}
  },
  computed() {
    return {
	}
  },
  props:{
    dimension: Array,
    title:{
      type:String,
	  default:'', 
    }
  },
  methods: {
	initSeries: function(options, dimension) {
	  let data = options.series[0].data;
	  data.length = 0;
	  let list = convertData(dimension);
	  for(let d of list) data.push(d);
	  
	  data = options.series[1].data;
	  data.length = 0;
	  for(let d of list) data.push(d);
	  
	  data = options.series[2].data;
	  data.length = 0;
	  list = dimension.map(function(dataItem) {
		return {
			name: dataItem.name,
			value: geoCoordMap[dataItem.name].concat([dataItem[key]])
		};
	  })
	  for(let d of list) data.push(d);
	},
	
	resize(width, height) {
	  this.$refs.cmap.resize({width: width, height: height});
	},
  },
  watch: {
    dimension: {
      handler(dimension) {
	    this.initSeries(this.options, dimension);
      },
      deep: true
	}
  },
  mounted: function(){
  },
}
</script>
