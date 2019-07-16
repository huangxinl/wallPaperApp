<template>
	<view class="content" @touchstart="start" @touchmove="move" @touchend="end">
		<scroll-view scroll-x class="nav text-center">
			<view class="cu-item" :class="0==TabCur?'theme-color cur':''" @tap="tabSelect" data-id="0">
				<text class="cuIcon-picfill"></text> 背景图
			</view>
			<view class="cu-item" :class="1==TabCur?'theme-color cur':''" @tap="tabSelect" data-id="1">
				<text class="cuIcon-evaluate_fill"></text> 头像
			</view>
			<view class="cu-item" :class="2==TabCur?'theme-color cur':''" @tap="tabSelect" data-id="2">
				<text class="cuIcon-emojiflashfill"></text> 表情
			</view>
		</scroll-view>
		
		<view v-if="0==TabCur" class="flexWapper">
			<block>
				<view class="card card-list2" v-for="(item,index) in imgList"  @click="goClassListJp(item)" :key="index">
					<image class="card-img card-list2-img" :src="img_root_jp+item.imgPath" mode="aspectFill" style="height: 200upx;"></image>
					<text class="card-num-view card-list2-num-view" style="background: #fa878a;">{{item.classify.classifyName}}</text>
					<view class="card-bottm">
						<view class="car-title-view">
							<text class="card-title card-list2-title">{{item.imgName}} </text>
						</view>
					<!-- 	<view @click.stop="share(item)" class="card-share-view"></view> -->
					</view>
				</view>
			</block>
		</view>
		
		<view v-if="1==TabCur"  class="flexWapper">
			<block>
				<view class="card card-list2" v-for="(item,index) in avatarList"  @click="goClassListJp(item)" :key="index">
					<image class="card-img card-list2-img" :src="img_root_jp+item.imgPath" mode="aspectFill" style="height: 200upx;"></image>
					<text class="card-num-view card-list2-num-view" style="background: #fa878a;">{{item.classify.classifyName}}</text>
					<view class="card-bottm">
						<view class="car-title-view">
							<text class="card-title card-list2-title">{{item.imgName}} </text>
						</view>
					<!-- 	<view @click.stop="share(item)" class="card-share-view"></view> -->
					</view>
				</view>
			</block>
		</view>
		
		<view v-if="2==TabCur"   class="flexWapper">
			<block>
				<view class="card card-list2" v-for="(item,index) in faceList"  @click="goClassListJp(item)" :key="index">
					<image class="card-img card-list2-img" :src="img_root_jp+item.imgPath" mode="aspectFill" style="height: 200upx;"></image>
					<text class="card-num-view card-list2-num-view" style="background: #fa878a;">{{item.classify.classifyName}}</text>
					<view class="card-bottm">
						<view class="car-title-view">
							<text class="card-title card-list2-title">{{item.imgName}} </text>
						</view>
					<!-- 	<view @click.stop="share(item)" class="card-share-view"></view> -->
					</view>
				</view>
			</block>
		</view>

		<view class="cu-list text-center cu-bar-loadmore">--------关注公众号：天天壁纸 获取更多内容--------</view>
		<text class="loadMore" v-if="!faceList.length">加载中...</text>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				TabCur: 0,
				scrollLeft: 0,
				PageCur: 'new',
				img_root:"https://bz.9188u.com/wallpaper_files/",
				img_root_jp: "https://jp.9188u.com/jp_wallpaper_files/",
				faceList: [],
				avatarList: [],
				imgList:[],
				data:[], //此处为图片列表使用
				refreshing: false,
				fetchPageNum: 1,
				pageX: null
			};
		},
		onLoad() {
			this.getImg();
			this.getAvatarList();
			this.getFaceList();
		},
		onReachBottom() {
			console.log('滑动到页面底部',this.TabCur)
			if(this.TabCur === 0) {
				console.log('背景')
				this.getImg();
			} else if(this.TabCur === 1) {
				console.log('头像')
				this.getAvatarList();
			} else if(this.TabCur === 2) {
				console.log('表情')
				this.getFaceList();
			}
		},
		onPullDownRefresh() {
			console.log('下拉刷新');
			this.refreshing = true;
			this.getImg();
		},
		methods: {
			start(e) {
				this.x = e.changedTouches[0].pageX
			},
			move(e) {
				this.pageX = this.x - e.changedTouches[0].pageX
			},
			end() {
				if(this.pageX > 150) {
					this.TabCur += 1
					if(this.TabCur === 3) {
						this.TabCur = 2
					}
				} else if(this.pageX < -150) {
					this.TabCur -= 1
					if(this.TabCur === -1) {
						this.TabCur = 0
					}
				}
			},
			tabSelect(e) {
				this.TabCur = e.currentTarget.dataset.id;
				this.scrollLeft = (e.currentTarget.dataset.id - 1) * 60
			},
			NavChange: function(e) {
				this.PageCur = e.currentTarget.dataset.cur;
				switch (e.currentTarget.dataset.cur){
					case 'home':
						uni.redirectTo({
							url: '../index/index'
						});
						break;
					case 'new':
						uni.showToast({
							title:'最新列表',
							icon: 'none',
						});
						break;
					case 'sort':
						uni.redirectTo({
							url: '../sort/sort'
						});
						break;
					case 'user':
						uni.redirectTo({
							url: '../user/my'
						});
						break;
				};
			},
			getAvatarList(){
				uni.request({
					url: "https://jp.9188u.com/getImg?size=21&classify=每日精选&page=" + (this.refreshing ? 1 : this.fetchPageNum),
					success: (res) => {
						if (res.statusCode !== 200) {
							console.log('请求失败。');
						} else {
							
							
							if (this.refreshing) {
								this.refreshing = false;
								uni.stopPullDownRefresh();
								this.avatarList = res.data.list;
								this.fetchPageNum = 2;
							} else {
								console.log(res.data.list)
								this.avatarList = this.avatarList.concat(res.data.list);
								this.fetchPageNum += 1;
							}
						}
						// this.imgList = res.data.list;
						console.log('avatarList:',this.avatarList)
					},
					fail: () => {
						uni.showModal({
							content: "请求失败，请重试!",
							showCancel: false
						})
					}
				})
			},
			getFaceList(){
				uni.request({
					url: "https://jp.9188u.com/getImg?size=21&classify=最新&page=" + (this.refreshing ? 1 : this.fetchPageNum),
					success: (res) => {
						console.log(res.data.list);
						if (res.statusCode !==200) {
							console.log('请求失败。');
						} else {
							if (this.refreshing) {
								this.refreshing = false;
								this.faceList = res.data.list;
								this.fetchPageNum = 2;
							} else {
								console.log(res.data.list);
								this.faceList = this.faceList.concat(res.data.list);
								this.fetchPageNum +=1;
							}
						}
						console.log('faceList:',this.faceList)
					},
					fail: () => {
						uni.showModal({
							content: "请求失败，请重试!",
							showCancel: false
						})
					}
				})
			},
			getImg() {
					uni.request({
						// https://jp.9188u.com/getImg?size=21&classify=最新&page=
					url: "http://jp.9188u.com/getImg?size=21&classify=背景图&page=" + (this.refreshing ? 1 : this.fetchPageNum),
					success: (res) => {
						if (res.statusCode !==200) {
							console.log('请求失败。');
						} else {
							if (this.refreshing && res.data.list[0].id === this.imgList[0].id) {
								uni.showToast({
									title:'已是最新列表',
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
								this.imgList = this.imgList.concat(res.data.list);
								this.fetchPageNum +=1;
							}
						}
						console.log('imglist:',this.imgList)
						// this.imgList = res.data.list;
					},
					fail: () => {
						uni.showModal({
							content: "请求失败，请重试!",
							showCancel: false
						})
					}
				})
			},
			goDetail(e) {
				console.log(e);
				uni.navigateTo({
					url: '../detail/detailindex?data=' + encodeURIComponent(JSON.stringify(e))
				})
			},
			goDetailJp(e) {
				uni.navigateTo({
					url: '../detail/singledetail?data=' + encodeURIComponent(JSON.stringify(e))
				})
			},
			goClassList(e) {
				console.log(e);
				uni.navigateTo({
					url: '../centerlist/centerlist?data=' + encodeURIComponent(JSON.stringify(e))
				})
			},
			goClassListJp(e) {
				console.log(e);
				uni.navigateTo({
					url: `../list/list?id=${e.classify.id}&name=${e.imgName}`
				})
			}
		}
	}
</script>

<style>
	.cu-bar-loadmore {
		min-height: 100upx;
	}
	.content {
		width: 100%;
	}
	.flexWapper {
		display: flex; 
		flex-direction: row;
		flex-wrap: wrap;
	}
	.theme-color {
		color: #fa878a;
	}
</style>
