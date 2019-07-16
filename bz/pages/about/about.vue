<template>
	<view class="about">
		{{List}}
		<view class="version">
			当前版本：{{version}}
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				List: '天天壁纸',
				version: '1.0.0',
			}
		}
	}
</script>

<style>
	page,
	view {
		display: flex;
	}

	page {
		min-height: 100%;
		background-color: #FFFFFF;
				text-align: center;
	}

	.about ,.version{
		font-size: 25upx;
		line-height: 40upx;
		padding: 30upx;
	}

</style>
