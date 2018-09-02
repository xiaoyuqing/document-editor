<template>
	<div class="document rolling-table">
		<div class="top-left"></div>
		<div class="table-head" :style="{overflow: 'hidden'}" ref="tFixedHead">
			<table id="top-table">
				<tr class="header"><th></th><th v-for="(item, index) in headers" :key="index">{{item}}</th></tr>
			</table>
		</div>
		<div class="left-fixed" ref="tFixedLeft" :style="{height: scrollHeight + 'px',paddingTop: '43px'}">
			<table id="left-table">
				<tr v-for="(item, index) in files" :key="index"><td>{{index+1}}</td></tr>
			</table>
		</div>
		<div id="scroll-table" class="scroll-table" ref="cScroll" :style="{height: scrollHeight + 'px'}">			
			<div class="table-body">
				<table id="roll-table" ref="scrollBody">
					<tr v-for="(tr, index) in files" :key="index">
						<td v-for="(item,index) in tr" :key="index">{{item.name}}</td>
					</tr>
				</table>
			</div>
		</div>		
	</div>
</template>
<script>
const iScoll = require('iscroll/build/iscroll-probe')
export default {
	props: {
		tableData: Object,
		pageSize: {
			type: Number,
			default: 11,
		}
	},
  data () {
    return {
			headers: [],
      files: [],
      showLeft: false,
			showTop: false,
			scrollHeight: window.innerHeight,
			pageNum: 1,
			dragStartX: null,
			dragStartY: null,
			refreshFlag: 0,
			leftTop: 0,
			topLeft: 0,
			moveCount: 200,
			lastTransformX: 0,
			lastTransformY: 0,
    }
  },
  mounted() {
		this.headers = this.tableData.headers
		let total = this.tableData.files.length
		this.totalPage = Math.ceil(total / this.pageSize)
		this.files = this.tableData.files.slice(0, this.pageSize)
		// this.$refs.cScroll.addEventListener('scroll', this.scrollTable)
		this.$refs.cScroll.addEventListener('touchstart', this.touchStart)
		this.$refs.cScroll.addEventListener('touchmove', this.touchMove)
  },
  methods: {
    scrollTable (e) {
			const clientHeight = e.target.clientHeight;
			const scrollHeight = e.target.scrollHeight;
			const scrollTop = event.target.scrollTop;
			if ((clientHeight + scrollTop) > scrollHeight - 63) {
				this.pageNum++;
				if (this.pageNum < this.totalPage || this.pageNum === this.totalPage) {
          this.files = this.tableData.files.slice(0, this.pageSize*this.pageNum);
				}
			}
			const _this = this
			let timer = null
			clearInterval(timer)
			timer = setTimeout(() => {
				_this.$refs.tFixedLeft.style.transform = 'translateY(' + -(e.target.scrollTop) + 'px)'			
				_this.$refs.tFixedHead.style.transform = 'translate(' + -(e.target.scrollLeft) + 'px, 0px)'			
			}, 10)
		},
		touchStart(event) {
			var self = this;
			if (self.refreshFlag) {
				event.preventDefault();
				return;
			}
			let reg = /\-?[0-9]+\.?[0-9]*/g
			let transform = document.getElementById('scroll-table').style.transform.match(reg)
			if (transform) {
				this.lastTransformX = parseInt(transform[0]);
				this.lastTransformY = parseInt(transform[1]);
			}
			event = event.touches[0];
			self.dragStartX = event.clientX;
			self.dragStartY = event.clientY;
			if (self.$refs.tFixedLeft.style.top) {
        self.leftTop = parseInt(self.$refs.tFixedLeft.style.top.replace(/px/g, ''));
			} else {
				self.leftTop = 0;
			}
			if (self.$refs.tFixedHead.style.left) {
        self.topLeft = parseInt(self.$refs.tFixedHead.style.left.replace(/px/g, ''));
			} else {
				self.topLeft = 0;
			}
		},
		touchMove(event) {
      var self = this;
			if (self.dragStart === null) {
				return;
			}
			event.preventDefault();
			if (self.refreshFlag) {
				event.preventDefault();
				return;
			}
			var target = event.touches[0];
			self.transformX = self.dragStartX - target.clientX;
			self.transformY = self.dragStartY - target.clientY;
			self.percentage = (self.dragStartY - target.clientY) / window.screen.height;
			var translateY = parseInt(-self.percentage * self.moveCount);
			// 左边            
			if ((self.leftTop - self.transformY) < 0) {
				self.$refs.tFixedLeft.style.top =  (self.leftTop - self.transformY) + 'px'
				document.getElementById('scroll-table').style.transform = 'translate(0,' + (self.leftTop - self.transformY) + 'px)';
			}
			// 头部
			if ((self.topLeft - self.transformX) < 0) {
				self.$refs.tFixedHead.style.left =  (self.topLeft - self.transformX) + 'px'
				document.getElementById('scroll-table').style.transform = 'translate(' + (self.topLeft - self.transformX) + 'px, 0)';
			}
			// if ((self.topLeft - self.transformX) < 0) {
      //   document.getElementById('scroll-table').style.webkitTransform = 
			//   document.getElementById('scroll-table').style.transform = 'translate(0,' + (self.$refs.tFixedHead.style.left) + 'px)';
			// }
		}
		
  }
}
</script>
<style lang="less" scoped>
// .document {
//   width: 100%;
//   position: relative;
// 	overflow: hidden;
// 	.table-head{
// 		position: absolute;
// 		overflow: hidden;
// 		z-index: 10;
// 	}
// 	.scroll-table{
// 		overflow: auto;
// 		-webkit-overflow-scrolling: touch;
// 		margin: 43px 0 0 63px;
// 	}
//   .top-left {
//     position: absolute;
//     top: 0px;
//     left: 0px;
//     width: 63px;
//     height: 43px;
//     z-index: 100;
//     background-color: #ddd;
//     border: 1px solid #eee;
//   }
//   table{
//     tr.header{
//     }
//     th{
//       min-width: 100px;
//       height: 40px;
//       background-color: #ddd;
//       border: 1px solid #eee;
//       text-align: center;
//       color: #888;
//     }
//     th:first-child{
//       min-width: 60px;
//     }
//     tr td{
//       min-width: 100px;
// 			border: 1px solid #eee;
// 			height: 60px;
//     }
// 	}
//   .left-fixed{
// 		position: absolute;
// 		top: 0px;
// 		left: 0px;
// 		overflow: hidden;
// 		z-index: 10;
// 		table#left-table {
// 			tr td:first-child{
// 				min-width: 60px;
// 				background-color: #ddd;
// 				border: 1px solid #eee;
// 				color: #888;
// 				text-align: center;
// 			}
//     }
// 	}
// }
</style>

