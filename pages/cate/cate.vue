<template>
	<view>
		<view class="search-box">
			<my-search @click="gotoSearch"></my-search>
		</view>
		<view class="scroll-view-container">
			<!-- 左侧滚动视图区域 -->
			<scroll-view scroll-y class="left-scroll-view" :style="{height:wh+'px'}">
				<block v-for="(item,index) in cateList" :key="index">
					<view :class="['left-scroll-view-item',active===index?'active':'']" @click="activeChanged(index)">
						{{item.cat_name}}
					</view>
				</block>
			</scroll-view>
			<!-- 右侧滚动视图区域 -->
			<scroll-view class="right-scroll-view" scroll-y :style="{height: wh + 'px'}" :scroll-top="scrollTop">
				<view class="cate-lv2" v-for="(item2,index2) in cateLevel2" :key="index2">
					<view class="cate-lv2-title">/ {{item2.cat_name}} /</view>
					<!-- 动态渲染三级分类列表的数据 -->
					<view class="cate-lv3-list">
						<view class="cate-lv3-item" v-for="(item3,index3) in item2.children" :key="index3"
							@click="gotoGoodsList(item3)">
							<image :src="item3.cat_icon.replace('dev','web')" mode=""></image>
							<text>{{item3.cat_name}}</text>
						</view>
					</view>
				</view>
			</scroll-view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				// 窗口可用高度= 屏幕高度 - navigationBar高度 - tabBar高度
				wh: 0,
				// 分类数据列表
				cateList: [],
				active: 0,
				// 二级分类列表
				cateLevel2: [],
				// 滚动条距离顶部的距离
				scrollTop: 0
			};
		},
		onLoad() {
			const sysInfo = uni.getSystemInfoSync()
			// 为 wh 窗口可用高度动态赋值
			this.wh = sysInfo.windowHeight
			this.getCateList()
		},
		methods: {
			async getCateList() {
				const {
					data: res
				} = await uni.$http.get('/api/public/v1/categories')
				// 判断是否获取失败
				if (res.meta.status !== 200) return uni.$showMsg()
				this.cateList = res.message
				// 为二级分类赋值
				this.cateLevel2 = res.message[0].children
				console.log('this.cateList', this.cateList);
				console.log('this.cateLevel2', this.cateLevel2);
			},
			// 切换商品
			activeChanged(index) {
				this.active = index
				this.cateLevel2 = this.cateList[index].children
				this.scrollTop = this.scrollTop === 0 ? 1 : 0
			},
			// 路由跳转到商品列表页面
			gotoGoodsList(item3) {
				console.log(item3);
				uni.navigateTo({
					url: '/subpkg/goods_list/goods_list?cid=' + item3.cat_id
				})
			},
			// 路由跳转到搜索页面
			gotoSearch() {
				uni.navigateTo({
					url: '/subpkg/search/search'
				})
			}
		}
	}
</script>

<style lang="scss">
	.scroll-view-container {
		display: flex;
		justify-content: space-around;

		.left-scroll-view {
			width: 120px;

			.left-scroll-view-item {
				line-height: 60px;
				background-color: #f7f7f7;
				text-align: center;
				font-size: 12px;

				&.active {
					background-color: #ffffff;
					position: relative;

					&::before {
						content: '';
						display: block;
						width: 3px;
						height: 30px;
						background-color: #c00000;
						position: absolute;
						left: 0;
						top: 50%;
						transform: translateY(-50%);
					}
				}
			}
		}

		.cate-lv2-title {
			text-align: center;
			font-size: 12px;
			font-weight: bold;
			padding: 15px 0;
		}

		.cate-lv3-list {
			display: flex;
			flex-wrap: wrap;

			.cate-lv3-item {
				width: 33.3%;
				margin-bottom: 10px;
				display: flex;
				flex-direction: column;
				align-items: center;

				image {
					width: 60px;
					height: 60px;
				}

				text {
					font-size: 12px;
				}
			}
		}
	}

	.search-box {
		position: sticky;
		top: 0;
		z-index: 999;
	}
</style>