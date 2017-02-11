<template>
    <echart ref="bar" :options="options" theme="darkyc" auto-resize>
</template>

<script>
import echarts from 'echarts/lib/echarts'

function defaultOptions() {
	return {
	  title: {
		text: '',
		left: '8%', 
	  },
	  legend: {
		data:['']
	  },
	  xAxis: [
	  {
		type : 'category',
		axisLine: {show: false},
		axisLabel: {show: true},
		axisTick: {show: false},
		splitLine: {show: false},
		data : [],
	  },
	  ],
	  yAxis: [{
		type: 'value',
		axisLine: {show: false},
		axisLabel: {show: false},
		axisTick: {show: false},
		splitLine: {show: false},
		max: 'dataMax',
	  }],
	  series: [],
	  dataZoom: [
	  {
		xAxisIndex: [0, 1],
		type: 'inside',
	  }],
	  grid: {
		left: '3%',
		right: '4%',
		bottom: '3%',
		containLabel: true
	  },
	  tooltip: {
		show: true,
	  }
	}
}

function getCategorieAxis() 
{
  let axis = {
	type : 'category',
	axisLine: {show: false},
	axisLabel: {show: true},
	axisTick: {show: false},
	splitLine: {show: false},
	data : [],
  }
  return axis;
}

 // For shadow
const shadow = 
{
	type: 'bar',
	itemStyle: {
		normal: {color: 'rgba(0,0,0,0.1)'}
	},
	barGap:'-100%',
	barCategoryGap:'40%',
	data: [],
    xAxisIndex: 1,
	silent: true, // tooltip
	animation: false
};

function getBarStyle(start, stop) {
	return {
		normal: {
			color: new echarts.graphic.LinearGradient(
				0, 0, 0, 1,
				[
					{offset: 0, color: start},
					{offset: 1, color: stop}
				]
			)
		},
		/*
		emphasis: {
			color: new echarts.graphic.LinearGradient(
				0, 0, 0, 1,
				[
					{offset: 0, color: '#2378f7'},
					{offset: 0.7, color: '#2378f7'},
					{offset: 1, color: '#83bff6'}
				]
			)
		}*/
	};
}
let barStyle = getBarStyle('#188df0', '#83bff6');

export default {
  data() {
    // init 
	let options = defaultOptions();
	if(this.dimensions.length) {
	  this.initSeries(options, this.dimensions)
	}
	if(this.categories.length) {
	  this.initCategories(options, this.categories);
	}
	options.title.text = this.title;
    return {
	  options: options
	}
  },
  computed() {
    return {
	}
  },
  props:{
    categories:Array, 
    dimensions:Array, 
    title:{
      type:String,
	  default:'', 
    }
  },
  methods: {
    initSeries: function(options, dimensions) {
	  // 初始化shadow
	  shadow.data.splice(0, shadow.data.length);
	  let max = -1;
	  let lm = 0;
	  if(options.series.length == 0) {
		//let series = dimensions.slice(0);
		// 使用unshift方法向array开头添加item
		options.series.unshift(shadow);
	  }
	  options.series.splice(1+dimensions.length, options.series.length); // 删除多余纬度
	  for(let i=0; i< dimensions.length; i++){
	    let s = options.series[i+1];
	    if(!s){
		  s = {};
		  s.name='';
		  s.data=[];
	      s.type = 'bar';
		  s.itemStyle = barStyle;
		  s.barCategoryGap = shadow.barCategoryGap;
		  options.series.push(s);
		}
		s.name = dimensions[i].name;
		s.data.splice(0, s.data.length);
		for(let d of dimensions[i].data)
		  s.data.push(d);
		
		// 计算最大值和最长数据列
		let m = Math.max.apply(Math, s.data);
		if(max == -1) {
		  max = m
		}else {
		  if(m > max) max = m;
		}
		let l = s.data.length;
		if(lm == 0) {
		  lm = l;
		}else{
		  if(l > lm) lm = l;
		}
	  }
	  while(lm --){
	    shadow.data.push(max);
	  }
	},
	
	initCategories: function(options, categories) {
	  options.xAxis.slice(categories.length, options.xAxis.length);
	  for(let i=0; i<categories.length; i++){
	    let cat = options.xAxis[i];
        if(!cat) {
		  cat = getCategorieAxis();
		  options.xAxis.push(cat);
		}
		cat.data.splice(0, cat.data.length);
		for(let d of categories[i].data)
		  cat.data.push(d);
	  }
	  /*
	  if(options.xAxis.length > 1)
	    options.dataZoom.xAxisIndex = [0, 1];
	  else{
	    options.dataZoom.xAxisIndex = [0];
	  }*/
	},
	
	resize(width, height) {
	  this.$refs.bar.resize({width: width, height: height});
	},
	
  },
  watch: {
    categories: {
      handler(categories) {
		this.initCategories(this.options, categories);
      },
      deep: true
    },
    dimensions: {
      handler(dimensions) {
		this.initSeries(this.options, dimensions);
      },
      deep: true
    },
  },
  mounted: function(){
  },
}
</script>
