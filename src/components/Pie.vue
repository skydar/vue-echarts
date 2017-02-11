<template>
    <echart :options="options" ref="pie" theme="darkyc" auto-resize></chart>
</template>

<script>
function defaultOptions() {
	return {
		title: {
			text: '',
			left: '8%', 
		},
		tooltip: {
			trigger: 'item',
			formatter: "{a} <br/>{b}: {c} ( {d}% )"
		},
		legend: {
			//orient: 'vertical',
			x: 'right',
			data:['']
		},
		series: [
			{
				name: '占比',
				type: 'pie',
				radius: ['30%', '70%'],
				center: ['50%', '50%'],
				avoidLabelOverlap: true,
				label: {
					normal: {
						show: true,
						position: 'inside',
						formatter: '{b}  ( {d}% )',
						textStyle: {
							fontSize: '14',
						}
					},
					emphasis: {
						show: true,
						textStyle: {
							fontSize: '16',
							fontWeight: 'bold'
						}
					}
				},
				labelLine: {
					normal: {
						show: false
					}
				},
				data:[
				  //{value:0, name:'name'},
				]
			}
		]
	};
}
export default {
  data() {
    // init 
	let options = defaultOptions();
	options.title.text = this.title;
	if(this.dimension.length){
	  this.initDimension(options, this.dimension);
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
    dimension:Array, 
    title:{
      type:String,
	  default:'', 
    }
  },
  methods: {
    initDimension: function(options, dimension) {
	  options.series[0].data.length = 0;
	  for(let d of dimension){
	    options.series[0].data.push(d);
	  }
	},
	
	resize(width, height) {
	  this.$refs.pie.resize({width: width, height: height});
	},
  },
  watch: {
    dimension: {
      handler(dimension) {
		this.initDimension(this.options, this.dimension);
      },
      deep: true
    },
  },
  mounted: function(){
  },
}
</script>
