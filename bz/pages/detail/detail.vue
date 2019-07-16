<template>
	<view class="index">
		<swiper @change="swpierChange" :style="{height:screenHeight + 'px'}">
			<swiper-item v-for="(item,index) in imgList" :key="index" @click="preImg(index)">
				<image :src="img_root + item.imgPath" mode="widthFix"></image>
			</swiper-item>
		</swiper>
		<!-- #ifndef H5 -->
		<view class="detail-btn-view">
			<view class="download" @click="download(ret,index)"></view>
			<!-- #ifdef APP-PLUS -->
			<!-- <view v-if="showBtn" class="setting" @click="setting">设为壁纸</view> -->
			<!-- #endif -->
			<!-- <view class="collect" @click="collect"></view> -->
		</view>
		<!-- #endif -->
	</view>
</template>

<script>
	export default {
		data() {
			return {
				ret: [],
				imgShow: false,
				index: 0,
				showBtn: false,
				screenHeight: 0,
				imgLength: 0,
				providerList: [],
				data: [],
				detailDec: "",
				imgList:[],
				img_root:"https://bz.9188u.com/wallpaper_files/",
			}
		},
		onLoad(e) {
			let data = JSON.parse(decodeURIComponent(e.data));
			console.log(data)
			this.getImg(data.id);
			uni.getProvider({
				service: "share",
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
					console.log("获取登录通道失败", e);
				}
			});
		},
		onShareAppMessage() {
			return {
				title: '',
				path: '/pages/detail/detail?data=' + this.detailDec,
				imageUrl: this.data[this.index]
			}
		},
		onNavigationBarButtonTap(e) {
			if (e.index === 0) {
				// #ifdef APP-PLUS
				if (this.providerList.length === 0) {
					uni.showModal({
						title: '当前环境无分享渠道!',
						showCancel: false
					})
					return;
				}
				let itemList = this.providerList.map(function(value) {
					return value.name
				})
				uni.showActionSheet({
					itemList: itemList,
					success: (res) => {
						uni.share({
							provider: this.providerList[res.tapIndex].id,
							scene: this.providerList[res.tapIndex].type && this.providerList[res.tapIndex].type === 'WXSenceTimeline' ?
								'WXSenceTimeline' : 'WXSceneSession',
							type: 0,
							title: '',
							summary: '',
							imageUrl: this.data[this.index],
							href: 'https://uniapp.dcloud.io',
							success: (res) => {
								console.log('success:' + JSON.stringify(res));
							},
							fail: (e) => {
								uni.showModal({
									content: e.errMsg,
									showCancel: false
								})
							}
						});
					}
				});
				// #endif
				// #ifdef H5
				this.collect();
				// #endif
			}
		},
		methods: {
			download(e,index) {
				console.log('item',e,index)
				let url = e[index].imgUrl
				uni.downloadFile({
					url,
					success: (e) => {
						if(e.statusCode == 200) {
							console.log('下载成功')
							uni.saveImageToPhotosAlbum({
								filePath: e.tempFilePath,
								success: () => {
									uni.showToast({
										icon: 'none',
										title: '已保存到手机相册'
									})
								},
								fail: () => {
									uni.showToast({
										icon: 'none',
										title: '保存到手机相册失败'
									})
								}
							});
						}
					},
					fail: (e) => {
						uni.showModal({
							content: '下载失败，' + e.errMsg,
							showCancel: false
						})
					}
				})
			},
			collect() {
				uni.showToast({
					icon: 'none',
					title: '点击收藏按钮'
				})
			},
			//#ifdef APP-PLUS
			setting() {
				uni.showToast({
					icon: 'none',
					title: '正在设为壁纸'
				})
				setTimeout(() => {
					var WallpaperManager = plus.android.importClass('android.app.WallpaperManager');
					var Main = plus.android.runtimeMainActivity();
					var wallpaperManager = WallpaperManager.getInstance(Main);
					plus.android.importClass(wallpaperManager);
					var BitmapFactory = plus.android.importClass('android.graphics.BitmapFactory');
					uni.downloadFile({
						url: this.data[this.index],
						success: (e) => {
							var filePath = e.tempFilePath.replace('file://', '');
							var bitmap = BitmapFactory.decodeFile(filePath);
							try {
								wallpaperManager.setBitmap(bitmap);
								uni.showToast({
									icon: 'none',
									title: '壁纸设置成功'
								})
							} catch (e) {
								uni.showToast({
									icon: 'none',
									title: '壁纸设置失败'
								})
							}
						},
						fail: () => {
							uni.showToast({
								icon: 'none',
								title: '壁纸设置失败'
							})
						}
					})
				}, 100)
			},
			//#endif
			swpierChange(e) {
				this.index = e.detail.current;
				// 设置导航头翻页数量和总数
				uni.setNavigationBarTitle({
					title: e.detail.current + 1 + '/' + this.imgLength
				})
			},
			preImg(index) {
				if (this.imgShow) { //防止点击过快导致重复调用 
					return;
				}
				this.imgShow = true;
				setTimeout(() => {
					this.imgShow = false;
				}, 1000)
				setTimeout(() => {
					uni.previewImage({
						current: this.data[index],
						urls: this.data
					})
				}, 150)
			},
			// 获取分类详情页信息
			getImg(e){
				console.log("开始获取数据");
				uni.request({
					url:"https://bz.9188u.com/getImg?page=1&size=20&classifyId=" + e,
					success: (res) =>{
						this.imgList = res.data.list;
						var ret = []
						this.imgList.forEach((item,index) => {
							ret.push({
								imgUrl: this.img_root + item.imgPath,
								index
							})
						})
						console.log(ret)
						this.ret = ret
						this.imgLength = this.imgList.length
					},
					fail:()=>{
						uni.showModal({
							content: "classRes请求失败，请重试!",
							showCancel: false
						})
					}
				})
			}
		}
	}
</script>

<style scoped>
	page {
		background-color: #000;
		height: 100%;
	}
	view {
		display: flex;
	}
	
	swiper {
		flex: 1;
		width: 750upx;
		background-color: #000;
		display: flex;
		flex-direction: column;
	}

	swiper-item {
		display: flex;
		align-items: center;
	}

	image {
		width: 750upx;
		height: 1125upx;
	}
</style>
