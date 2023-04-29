<template>
	<view class="">
		<view class="search-box">
			<!-- 使用uni-search搜索组件 -->
			<uni-search-bar @input="input" :radius="100" cancelButton="none"></uni-search-bar>
		</view>
		<view class="sugg-list" v-if="searchResults.length !== 0">
			<view class="sugg-item" v-for="(item,index) in searchResults" :key="item.goods_id"
				@click="gotoDetail(item.goods_id)">
				<view class="goods-name">{{item.goods_name}}</view>
				<uni-icons type="arrowright" size="16"></uni-icons>
			</view>
		</view>
		<!-- 搜索历史 -->
		<view class="history-box" v-else>
			<view class="history-title">
				<text>搜索历史</text>
				<uni-icons type="trash" size="17" @click="cleanHistory"></uni-icons>
			</view>
			<!-- 列表区域 -->
			<view class="history-list">
				<uni-tag :text="item" v-for="(item, i) in historyList" :key="i" @click="gotoGoodsList(item)"></uni-tag>
			</view>
		</view>
	</view>

</template>

<script>
	export default {
		data() {
			return {
				// 延时器的timerId
				timer: null,
				// 搜索关键词
				kw: '',
				// 搜索结果列表
				searchResults: [],
				historyList: ['a', 'app', 'apple']
			};
		},
		onLoad() {
			this.historyList = JSON.parse(uni.getStorageSync('kw') || '[]')
		},
		methods: {
			input(e) {
				clearTimeout(this.timer)
				this.timer = setTimeout(() => {
					this.kw = e
					this.getSearchList()
					console.log(this.kw);
				}, 500)
			},
			// 根据搜索关键词，搜索商品建议列表
			async getSearchList() {
				if (this.kw == '') {
					this.searchResults = []
					return
				}
				// 发起请求，获取搜索建议列表
				const {
					data: res
				} = await uni.$http.get('/api/public/v1/goods/qsearch', {
					query: this.kw
				})
				this.saveHistory(this.kw)
				if (res.meta.status !== 200) return uni.$showMsg()
				this.searchResults = res.message
				console.log('this.searchResults', this.searchResults);
			},
			// 路由跳转到商品详情
			gotoDetail(goods_id) {
				uni.navigateTo({
					url: '/subpkg/goods_detail/goods_detail?goods_id=' + goods_id
				})
			},
			// 保存搜索关键词到历史列表中
			saveHistory(kw) {
				// 利用集合，清除重复的关键词
				this.historyList.push(kw)
				let set = new Set(this.historyList)
				this.historyList = Array.from(set)
				// 保存关键词为历史记录
				uni.setStorageSync('kw', JSON.stringify(this.historyList))
			},
			// 清除历史记录
			cleanHistory() {
				this.historyList = []
				uni.setStorageSync('kw', '[]')
			},
			// 点击跳转到商品列表页面
			gotoGoodsList(kw) {
				uni.navigateTo({
					url: '/subpkg/goods_list/goods_list?query=' + kw
				})
			}
		}
	}
</script>

<style lang="scss">
	.search-box {
		position: sticky;
		top: 0;
		z-index: 999;
	}

	.sugg-list {
		padding: 0 5px;

		.sugg-item {
			font-size: 12px;
			padding: 13px 0;
			border-bottom: 1px solid #efefef;
			display: flex;
			align-items: center;
			justify-content: space-between;

			.goods-name {
				white-space: nowrap;
				overflow: hidden;
				text-overflow: ellipsis;
				margin-right: 3px;
			}
		}
	}

	.history-box {
		padding: 0 5px;

		.history-title {
			display: flex;
			justify-content: space-between;
			align-items: center;
			height: 40px;
			font-size: 13px;
			border-bottom: 1px solid #efefef;
		}

		.history-list {
			display: flex;
			justify-content: flex-end;
			flex-wrap: wrap;

			.uni-tag {
				margin-top: 5px;
				margin-right: 5px;
			}
		}
	}
</style>