<template>
	<div class="document rolling-table">
		<div class="top-left"></div>
		<div class="table-head" :style="{position: 'absolute', overflow: 'hidden'}">
			<table id="top-table" ref="tFixedHead">
				<tr class="header"><th></th><th v-for="(item, index) in headers" :key="index">{{item}}</th></tr>
			</table>
		</div>
		<div class="left-fixed" :style="{height: scrollHeight + 'px',paddingTop: '43px'}">
			<table id="left-table" ref="tFixedLeft">
				<tr v-for="(item, index) in files" :key="index"><td>{{index+1}}</td></tr>
			</table>
		</div>
		<div class="scroll-table" ref="cScroll" :style="{height: scrollHeight + 'px'}">			
			<div class="table-body" style="margin: 43px 0 0 63px;">
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
    }
  },
  mounted() {
		this.headers = this.tableData.headers
		let total = this.tableData.files.length
		this.totalPage = Math.ceil(total / this.pageSize)
		this.files = this.tableData.files.slice(0, this.pageSize)
		this.$refs.cScroll.addEventListener('scroll', this.scrollTable)
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
		}
  }
}
</script>
<style lang="less" scoped>
.document {
  width: 100%;
  position: relative;
	overflow: hidden;
	.table-head{
		position: absolute;
		overflow: hidden;
		z-index: 99;
	}
	.scroll-table{
		overflow: auto;
		-webkit-overflow-scrolling: touch;	
	}
  .top-left {
    position: absolute;
    top: 0px;
    left: 0px;
    width: 63px;
    height: 43px;
    z-index: 100;
    background-color: #ddd;
    border: 1px solid #eee;
  }
  table{
    tr.header{
    }
    th{
      min-width: 100px;
      height: 40px;
      background-color: #ddd;
      border: 1px solid #eee;
      text-align: center;
      color: #888;
    }
    th:first-child{
      min-width: 60px;
    }
    tr td{
      min-width: 100px;
			border: 1px solid #eee;
			height: 60px;
    }
  }
  .left-fixed{
		position: absolute;
		top: 0;
		left: 0;
		overflow: hidden;
		z-index: 99;
		table#left-table {
			tr td:first-child{
				min-width: 60px;
				background-color: #ddd;
				border: 1px solid #eee;
				color: #888;
				text-align: center;
			}
    }
	}
	.table-header{
    position: absolute;
    overflow: hidden;
	}
}
</style>

