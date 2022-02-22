<template>
	<scroll-view ref='list' scroll-y="true" class='list-container' @scroll="handleScroll" style="height: 100vh;">
		<div class='list-phantom'  :style="{ height: listHeight + 'px' }"></div>
		<div class='list' :style="{ transform: getTransform }">
			<div v-for="(item,index) in visibleData" class='list-item' :key='item.id' :style="{height:itemSize+'px',lineHeight:itemSize+'px'}">
				{{item.value}}
			</div>
		</div>
	</scroll-view>
</template>

<script>
	export default {
		props:{
			//所有列表数据
		   listData:{
		     type:Array,
		     default:()=>[]
		   },
		   //每项高度
		   itemSize: {
		     type: Number,
		     default:200
		   }	
		},
		computed:{
			//列表总高度
		    listHeight(){
		      return this.listData.length * this.itemSize;
		    },	
			//可显示的列表项数
		    visibleCount(){
		      return Math.ceil(this.screenHeight / this.itemSize)
		    },	
			//偏移量对应的style
		    getTransform(){
		      return `translate3d(0,${this.startOffset}px,0)`;
		    },	
			//获取真实显示列表数据
		    visibleData(){
		      return this.listData.slice(this.startIndex, Math.min(this.endIndex,this.listData.length));
		    }	
		},
		data() {
			return {
				screenHeight:0, // 屏幕高度即可视区域高度
				startOffset:0, // 顶部偏移量
				startIndex:0, // 可视化区域的数据开始下标
				endIndex:0, // 可视化区域的数据结束下标
			}
		},
		mounted() {
			this.getScreenHeight()
			this.startIndex = 0;
			this.endIndex = this.startIndex + this.visibleCount;
			console.log(this.visibleData)
		},
		methods: {
			// 获取屏幕高度即可视化区域高度
			getScreenHeight(){
				const _this=this
				uni.getSystemInfo({
				    success: function (res) {
						_this.screenHeight=res.screenHeight
				    }
				});
			},
			handleScroll(e){
				//当前滚动位置
				let scrollTop = e.detail.scrollTop;
				//开始索引
				this.startIndex = Math.floor(scrollTop / this.itemSize);	 
				//结束索引
				this.endIndex = this.startIndex + this.visibleCount;
				//顶部偏移量
				this.startOffset = scrollTop - (scrollTop % this.itemSize);
			}
		}
	}
</script>

<style lang="scss" scoped>
.list-container{
	 height: 100%;
	  overflow: auto;
	  position: relative;
	.list-phantom{
		position: absolute;
		  left: 0;
		  top: 0;
		  right: 0;
		  z-index: -1;
	}
	.list{
		  left: 0;
		  right: 0;
		  top: 0;
		  position: absolute;
		.list-item{
			text-align: center;
			border-bottom: 1px solid #ccc;
		}
	}
}
</style>
