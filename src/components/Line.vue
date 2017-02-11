<template>
    <echart ref="line" :options="options" theme="darkyc" auto-resize>
</template>

<script>
function defaultOptions() {
	return {
	  title: {
		text: '',
		left: '8%', 
	  },
	  legend: {
		data:[],
		right:'30',
		top:'30',
	  },
	  xAxis: [{
		axisLine: {show: true},
		axisLabel: {show: true},
		axisTick: {show: false},
		splitLine: {show: false},
		boundaryGap: false,
		axisLabel : {rotate:45, interval: 0,},
		data : [],
	  }],
	  yAxis: [{
		type: 'value',
		axisLine: {show: false},
		axisLabel: {show: true},
		axisTick: {show: false},
		splitLine: {show: true, lineStyle: {type: 'solid'},},
		max: 'dataMax',
	  }],
	  series: [],
	  dataZoom: [
	  {
		type: 'inside',
	  }],
	  grid: {
		left: '3%',
		right: '4%',
		bottom: '5%',
		containLabel: true
	  },
	  tooltip: {
		trigger: 'axis'
	  }
	};
}

const lineStyle = {
	normal: {}
};

export default {
  data() {
    // init 
	let options = defaultOptions();
	if(this.dimensions.length) {
	  this.initSeries(options, this.dimensions)
	}
	if(this.category.length) {
	  this.initCategory(options, this.category);
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
    category:Array, 
    dimensions:Array, 
    title:{
      type:String,
	  default:'', 
    }
  },
  methods: {
    initSeries: function(options, dimensions) {
	  options.series.splice(dimensions.length, options.series.length);
	  options.legend.data.length = 0
	  for(let i=0; i<dimensions.length; i++){
	    let s = options.series[i];
	    if(!s){
		  s = {};
		  s.name='';
		  s.data=[];
		  s.type = 'line';
		  s.areaStyle = lineStyle;
          s.stack = 'total';
		  options.series.push(s);
		}
		s.name = dimensions[i].name;
		s.data.splice(0, s.data.length);
		for(let d of dimensions[i].data)
		  s.data.push(d);
		options.legend.data.push(s.name);
	  }
	},
	initCategory: function(options, category) {
	  options.xAxis[0].data.length = 0;
	  for(let cat of category){
	    options.xAxis[0].data.push(cat);
	  }
	},
	
	resize(width, height) {
	  this.$refs.line.resize({width: width, height: height});
	},
  },
  watch: {
    category: {
      handler(category) {
		this.initCategory(this.options, category);
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
