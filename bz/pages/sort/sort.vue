<template>
	<view class="index">
		<block v-for="item in list" :key="item.id">
			<view class="card" @click="goDetail(item)">
				<image class="card-img" :src="img_root + item.imgPath" mode="widthFix"></image>
				<text class="card-num-view" style="background: #fa878a;">{{item.classifyName}}</text>
				<view class="card-bottm row">
					<view class="car-title-view row">
						<text class="card-title" >{{item.title}}</text>
					</view>
				<!-- 	<view @click.stop="share(item)" class="card-share-view"></view> -->
				</view>
			</view>
		</block>
		<text class="loadMore" v-if="!list.length">加载中...</text>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				refreshing: false,
				providerList: [],
				list: [],
				fetchPageNum: 1,
				img_root: "https://bz.9188u.com/wallpaper_files/",
			}
		},
		onLoad() {
			this.getData();
			uni.getProvider({
				service: 'share',
				success: (e) => {
					let data = []
					for (let i = 0; i < e.provider.length; i++) {
						switch (e.provider[i]) {
							case 'weixin':
								data.push({
									name: '分享到微信好友',
									id: 'weixin'
								})
								data.push({
									name: '分享到微信朋友圈',
									id: 'weixin',
									type: 'WXSenceTimeline'
								})
								break;
							case 'qq':
								data.push({
									name: '分享到QQ',
									id: 'qq'
								})
								break;
							default:
								break;
						}
					}
					this.providerList = data;
				},
				fail: (e) => {
					console.log('获取分享通道失败', e);
				}
			});
		},
		onReachBottom() {
			console.log('滑动到页面底部')
			this.getData();
		},
		onPullDownRefresh() {
			console.log('下拉刷新');
			this.refreshing = true;
			setTimeout(() => {
				uni.stopPullDownRefresh()
			},20)
		},
		methods: {
			getData() {
				// uni.request({
				// 	url: this.$serverUrl + '/api/picture/posts.php?page=' + (this.refreshing ? 1 : this.fetchPageNum) +
				// 		'&per_page=5',
				// 	success: (ret) => {
				// 		console.log('data', ret);
				// 		if (ret.statusCode !== 200) {
				// 			console.log('失败!');
				// 		} else {
				// 			if (this.refreshing && ret.data[0].id === this.list[0].id) {
				// 				uni.showToast({
				// 					title: '已经最新',
				// 					icon: 'none',
				// 				})
				// 				this.refreshing = false;
				// 				uni.stopPullDownRefresh();
				// 				return;
				// 			}
				// 			if (this.refreshing) {
				// 				this.refreshing = false;
				// 				uni.stopPullDownRefresh()
				// 				this.list = ret.data;
				// 				this.fetchPageNum = 2;
				// 			} else {
				// 				this.list = this.list.concat(ret.data);
				// 				this.fetchPageNum += 1;
				// 			}
				// 		}
				// 	}
				// })
					uni.request({
						url: 'https://jp.9188u.com/cl/getRMZJ',
						success: (res) => {
							if (res.statusCode !== 200) {
								console.log('失败!');
							} else {
								if (this.refreshing && res.data[0].id === this.list[0].id) {
									uni.showToast({
										title: '已经最新',
										icon: 'none',
									})
									this.refreshing = false;
									uni.stopPullDownRefresh();
									return;
							}
							// console.log(res.data.list)
							this.list = res.data.list
						}
					 }
					})
			},
			goDetail(e) {
				console.log(e)
				uni.navigateTo({
					url: '../detail/detail?data=' + encodeURIComponent(JSON.stringify(e))
				})
			}
		}
	}
</script>

<style>

</style>
