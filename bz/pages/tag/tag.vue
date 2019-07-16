<template>
	<view class="tags">
		<block v-for="value in list" :key="value.id">
			<view class="tag" @tap="goList(value)">
				<image class="tag-img" :src="imgRoot + value.imgPath" mode="aspectFill"></image>
				<text class="tag-text">{{ value.classifyName }}</text>
			</view>
		</block>
	</view>
</template>

<script>
	let index = 0;
	export default {
		data() {
			return {
				imgRoot: 'https://jp.9188u.com/jp_wallpaper_files/',
				list: []
			};
		},
		created() {
			this._getClassify()
		},
		methods: {
			goList(value) {
				console.log(value.classifyName)
				console.log('-------',value.id)
				uni.navigateTo({
					url: `../list/list?id=${value.id}&name=${value.classifyName}`
				});
			},
			_getClassify() {
				uni.request({
					// https://bz.9188u.com/cl/getClassifyByClassifyName?classify=%E5%88%86%E7%B1%BB
					
					url: 'https://jp.9188u.com/cl/getClassifyByClassifyName?classify=壁纸', 
					success: (res) => {
						this.list = res.data.list.slice(0,8)
						console.log('---list----',this.list)
					}
				});
			}
		}
	};
</script>

<style>
	.tags {
		display: flex;
		height: 400upx;
		border-radius: 5px;
	}
	.tag-text {
		font-size: 20upx;
		color: #8799A3;
		margin-top: 10upx;
	}
	.tag {
		/* margin: 0 26upx; */
	}
	
	.tag-img {
	    box-shadow:2px 2px 2px #8799A3;
	} 
</style>
