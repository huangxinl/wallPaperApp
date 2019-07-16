<template>
	<view class="index">
		<block v-for="(item,index) in lists" :key="index">
			<view class="row">
				<view class="card card-list2"  @click="goDetail(item)">
					<image class="card-img card-list2-img" :src="imgRoot + item.imgPath"></image>
					<text class="card-num-view card-list2-num-view" style="background: #fa878a;">{{item.imgName}}</text>
					<view class="card-bottm row">
						<view class="car-title-view row">
							<text class="card-title card-list2-title">{{item.classify.classifyName}}</text>
						</view>
					</view>
				</view>
			</view>
		</block>
		<text class="loadMore" v-if="!lists.length">{{loadMoreText}}</text>
	</view>
</template>

<script>
	let keys = 0;
	export default {
		data() {
			return {
				refreshing: false,
				loadMoreText: '加载中...',
				lists: [],
				id: 0,
				fetchPageNum: 0,
				imgRoot: 'https://jp.9188u.com/jp_wallpaper_files/'
			}
		},
		onLoad(e) {
			console.log('----------', e.id)
			console.log('----------', e.name)
			uni.setNavigationBarTitle({
				title: '专题：' + e.name
			});
			this.id = e.id;
			// 防止app里由于渲染导致转场动画卡顿
			setTimeout(() => {
				this.getData();
			}, 300);
			uni.getProvider({
				service: 'share',
				success: (e) => {
					let data = [];
					for (let i = 0; i < e.provider.length; i++) {
						switch (e.provider[i]) {
							case 'weixin':
								data.push({
									name: '分享到微信好友',
									id: 'weixin'
								});
								data.push({
									name: '分享到微信朋友圈',
									id: 'weixin',
									type: 'WXSenceTimeline'
								});
								break;
							case 'qq':
								data.push({
									name: '分享到QQ',
									id: 'qq'
								});
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
		onPullDownRefresh() {
			console.log('下拉刷新');
			this.refreshing = true;
			this.getData();
		},
		onReachBottom() {
			console.log('上拉加载刷新');
			if (this.fetchPageNum > 4) {
				this.loadMoreText = '没有更多了'
				return;
			}
			this.getData();
		},
		methods: {
			goDetail(e) {
				uni.navigateTo({
					url: '../detail/singledetail?data=' + encodeURIComponent(JSON.stringify(e))
				});
			},
			getData() {
				uni.request({
					url: `https://jp.9188u.com/getImg?page=1&size=5&classifyId=${this.id}`,
					success: (res) => {
						if (res.statusCode !== 200) {
							console.log('请求失败', res)
						} else {
							if (this.refreshing && res.data.list[0].id === this.lists[0].id) {
								uni.showToast({
									title: '已是最新列表',
									icon: 'none',
								})
								this.refreshing = false;
								uni.stopPullDownRefresh();
								return;
							}
							if (this.refreshing) {
								this.refreshing = false;
								uni.stopPullDownRefresh();
								this.imgList = res.data.list;
								this.fetchPageNum = 2;
							} else {
								this.lists = this.lists.concat(res.data.list);
								this.fetchPageNum += 1;
							}
							console.log('--------------eeee', this.lists)
						}

					}
				})
			}
		}
	}
</script>

<style>
	.index {
		display: flex;
		flex-direction: row;
		flex-wrap: wrap;
	}

	.row {
		flex: 1;
	}

	.card-title {
		text-align: center;
	}
</style>
