<template>
    <echart :options="options" ref="nmap" theme="darkyc" auto-resize></chart>
</template>

<script>
import echarts from './yinchuan.js'

var geoCoordMap = {
	"西夏区": [106.132116, 38.492424],
	"兴庆区": [106.278393, 38.46747],
	"金凤区": [106.228486, 38.477353],
	"永宁县": [106.253781, 38.28043],
	"贺兰县": [106.345904, 38.554563],
	"灵武市": [106.334701, 38.094058],
}
var markPositionMap = {
	"西夏区": {x: '30%', y: '15%'},
	"兴庆区": {x: '65%', y: '35%'},
	"金凤区": {x: '70%', y: '50%'},
	"永宁县": {x: '25%', y: '60%'},
	"贺兰县": {x: '60%', y: '10%'},
	"灵武市": {x: '40%', y: '80%'},
}

function defaultOptions() {
	let options = {
		title: {
			text: '',
			left: '8%', 
		},
		tooltip: {
			trigger: 'item',
            showDelay: 0,
            transitionDuration: 0.2,
            formatter: function (params) {
                var value = (params.value + '').split('.');
                value = value[0].replace(/(\d{1,3})(?=(?:\d{3})+(?!\d))/g, '$1,');
                return params.seriesName + '<br/>' + params.name + ': ' + value;
            }
		},
		geo: {
			name: '地图',
			map: 'yinchuan',
			roam: false,
			zoom: 1,
			label: {
				normal: {
					show: false,
				},
				emphasis: {
					show: false,
				}
			},
			itemStyle: {
				normal: {
					areaColor: '#323C48',
					borderColor: '#404A59'
				},
				emphasis: {
					show: false,
					areaColor: '#323C48',
				}
			}
		},
		visualMap: {
			type: 'continuous',
			show: false,
			min: 0,
			max: 1,
			text:['High','Low'],
			realtime: false,
			color: ['orangered','yellow','lightskyblue']
		},
		series: [
            {
                name: '区县分布',
                type: 'map',
				roam: false,
				zoom: 1,
                map: 'yinchuan',
                label: {
					normal: {
						show: false,
					},
					emphasis: {
						show: false,
					}
				},
				itemStyle: {
					normal: {
						areaColor: '#323C48',
						borderColor: '#404A59'
					},
					emphasis: {
						show: false,
						areaColor: '#323C48',
					}
				},
                data: [],
				markLine: {
					silent: true,
					symbol: ['circle', 'image://url'], //'image://url'
					symbolSize: 12, // 不能设置两头
					label: {
						normal: {
							show: true,
							position: 'end',
							formatter: '{b}: {c}',
							textStyle: { // 文档上居然没有说明..
								fontSize: '14',
							}
						},
						emphasis: {
							show: false,
						}
						
					},
					lineStyle: {
						normal: {
							show: true,
							type: 'solid',
							color: '#fff',
						},
						emphasis: {
							show: false,
						},
					},
					data: [],
				},
            },
        ]
	};
	return options;
}


export default {
  data() {
    // init 
	let options = defaultOptions();
	options.title.text = this.title;
	this.initSeries(options, this.dimension);
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
	  let max = 0;
	  data.length = 0;
	  for(let d of dimension) {
	    data.push(d);
		if(max == 0) {
		  max = d.value;
		}else {
		  if(max < d.value){
		    max = d.value;
		  }
		}
	  }
	  options.visualMap.max = max;
	  
	  data = options.series[0].markLine.data;
	  data.length = 0;
	  var list = dimension.map(function(dataItem) {
		var geoCoord = geoCoordMap[dataItem.name];
		if (geoCoord) {
		  return [{
			name: dataItem.name,
			coord: geoCoord,
			value: dataItem.value,
		  }, markPositionMap[dataItem.name]];
	    }else {
		  return [];
		}
	  });
	  for(let d of list) data.push(d);
	  
	},
	resize(width, height) {
	  this.$refs.nmap.resize({width: width, height: height});
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
