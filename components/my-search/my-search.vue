<template>
	<view class="my-search-container" :style="{'background-color':bgcolor}">
		<!-- 使用view组件模拟input输入框 -->
		<view class="my-search-box" :style="{'border-radius':radius+'px'}" @click="searchBoxHandler">
			<uni-icons type="search" size="15"></uni-icons>
			<text class="placeholder">搜索</text>
		</view>
	</view>
</template>

<script>
	export default {
		name: "my-search",
		data() {
			return {

			};
		},
		props: {
			// 背景颜色
			bgcolor: {
				type: String,
				default: '#C00000'
			},
			// 圆角尺寸
			radius: {
				type: Number,
				default: 18
			}
		},
		onLoad() {
			const sysInfo = uni.getSystemInfoSync()
			this.wh = sysInfo.windowHeight - 50
		},
		methods: {
			// 点击了模拟的 input 输入框
			searchBoxHandler() {
				// 触发外界通过 @click 绑定的 click 事件处理函数
				this.$emit('click')
			}
		}
	}
</script>

<style lang="scss">
	.my-search-container {
		// background-color: #c00000;
		height: 50px;
		padding: 0 10px;
		display: flex;
		align-items: center;
	}

	.my-search-box {
		height: 36px;
		background-color: #fff;
		// border-radius: 15px;
		width: 100%;
		display: flex;
		align-items: center;
		justify-content: center;
	}

	.placeholder {
		font-size: 15px;
		margin-left: 5px;
	}
</style>