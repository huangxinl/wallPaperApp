<template>
	<view class="index">
		<swiper @change="getNext" @animationfinish="getNext" :style="{height:screenHeight + 'px'}">
			<swiper-item v-for="(item,index) in imgList" :key="index" @click="preImg(index)">
				<image :src="item.url" mode="widthFix"></image>
			</swiper-item>
		</swiper>
		<!-- #ifndef H5 -->
		<view class="detail-btn-view">
			<view class="download" @click="download(url)"></view>
			<!-- #ifdef APP-PLUS -->
		<!-- 	<view v-if="showBtn" class="setting" @click="setting">设为壁纸</view> -->
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
				loading: false,
				downloadProgress: '',
				first: true,
				img_root: "https://bz.9188u.com/wallpaper_files/",
				classify_root: "https://bz.9188u.com/wallpaper_files/classify_img/",
				id: "",
				classifyID: "",
				avatar: "",
				url: "",
				addTime: '',
				imgName: '',
				screenHeight: 0,
				describe: "",
				clicks: "",
				imgList: [{
					id: "",
					classifyID: "",
					avatar: "",
					url: "",
					addTime: '',
					imgName: '',
					screenHeight: 0,
					describe: "",
					clicks: "",
				}],
			}
		},
		onLoad(e) {
			this.screenHeight = uni.getSystemInfoSync().windowHeight - uni.upx2px(200);
			let data = JSON.parse(decodeURIComponent(e.data));
			console.log('---进来的数据------',data)
			this.imgList[0].avatar = "background-image:url(" + this.classify_root + data.classify.imgPath + ");";
			this.avatar = "background-image:url(" + this.classify_root + data.classify.imgPath + ");";
			this.imgList[0].url = this.img_root + data.imgPath;
			this.url = this.img_root + data.imgPath;
			this.imgList[0].addTime = data.addTime;
			this.addTime = data.addTime;
			this.imgList[0].imgName = data.imgName;
			this.imgName = data.imgName;
			this.imgList[0].describe = data.describe;
			this.describe = data.describe;
			this.imgList[0].clicks = data.clicks;
			this.clicks = data.clicks;
			this.imgList[0].id = data.id;
			this.id = data.id
			this.imgList[0].classifyID = data.classify.id;
			this.classifyID = data.classify.id;
			console.log('------',this.imgList,this.imgList.length)
			this.getNext();
			uni.setNavigationBarTitle({
				title: this.imgList[0].imgName
			});
		},
		methods: {
			download(e) {
				console.log(e)
				var downURL = e.split('_zip');
				const downloadTask = uni.downloadFile({
					url: downURL.join(''), //仅为示例，并非真实的资源
					success: (res) => {
						if (res.statusCode === 200) {
							console.log(res.tempFilePath);
							// this.loading = false;  //用于取消进度条。
							uni.saveImageToPhotosAlbum({
								filePath: res.tempFilePath,
								success: function() {
									console.log('save success');
									uni.showModal({
										content: "下载成功",
										showCancel: false
									});
								},
								fail: () => {
									console.log("保存失败");
									uni.showModal({
										content: "保存失败",
										showCancel: false
									});
								}
							});
						}
					},
					fail: () => {
						uni.showModal({
							content: "下载请求失败，稍候再重试。",
							showCancel: false
						})
					}
				})
			},
			getNext(e) {
				// console.log(e);
				if (this.first) {
					var index = 0;
					this.first = false;
				} else {
					var index = e.detail.current;
				}
				this.avatar = this.imgList[index].avatar;
				this.describe = this.imgList[index].describe;
				this.clicks = this.imgList[index].clicks;
				this.imgName = this.imgList[index].imgName;
				this.url = this.imgList[index].url;
				var next = parseInt(index) + 1;
				console.log('next index',next,index)
				console.log('id',this.imgList[index].id);
				uni.request({
					url: "https://bz.9188u.com/getOtherImg?imgId=" + this.imgList[index].id + "&fx=right&classifyId=" + this.imgList[
						index].classifyID,
					success: (nextRes) => {
						// this.nextRes = nextRes.data.list;
						console.log("这里开始");
						console.log('resArr----',nextRes.data);
						var resArr = [{
							avatar: '',
							url: '',
							addTime: '',
							imgName: '',
							describe: '',
							clicks: '',
							id: '',
							classifyID: ''
						}];
						resArr[0].avatar = "background-image:url(" + this.classify_root + nextRes.data.img.classify.imgPath + ");";
						resArr[0].url = this.img_root + nextRes.data.img.imgPath;
						resArr[0].addTime = nextRes.data.img.addTime;
						resArr[0].imgName = nextRes.data.img.imgName;
						resArr[0].describe = nextRes.data.img.describe;
						resArr[0].clicks = nextRes.data.img.clicks;
						resArr[0].id = nextRes.data.img.id;
						resArr[0].classifyID = nextRes.data.img.classify.id;
						console.log('合并之前imgList',this.imgList)
						console.log('len next',this.imgList.length,next)
						if (this.imgList.length == next) {
							this.imgList = this.imgList.concat(resArr);
						}

						console.log('合并之后imgList',this.imgList);
					},
					fail: () => {
						uni.showModal({
							content: "nextRes请求失败，请重试!",
							showCancel: false
						})
					}
				})
			}
		}
	}
</script>

<style scoped>
	.cu-card {
		width: 100%;
	}
	.title {
		font-size: 12px;
		flex: 1;
		color: #FFFFFF;
	}
	.btnChoose {
		position:fixed;
		bottom:150px;
		right:21px;
		z-index:1000;
	}
	.btn-click {
		display: flex;
		flex-direction: column;
		padding:10px 0 ;
		background: rgb(255, 189, 187);
		border-radius: 50%;
		width:50px;
		height:50px;
		margin: 5px;
		text-align: center;
		
	}
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
