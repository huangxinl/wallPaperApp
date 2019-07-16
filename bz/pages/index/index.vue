<template>
	<view class="index">
		<!-- <nav-swipper :imgList="swipperList"></nav-swipper> -->
		<tag v-if="imgList.length"></tag>
		<view class="row">
			<block v-for="list in imgList" :key="list.id">
				<view class="card card-list2" v-for="(item,index) in imgList" @click="goDetail(item)" :key="index">
					<image class="card-img card-list2-img" :src="img_root+item.imgPath" mode="aspectFill"></image>
					<text class="card-num-view card-list2-num-view " style="background: #fa878a;">{{item.classify.classifyName}}</text>
					<view class="card-bottm">
						<view class="car-title-view">
							<text class="card-title card-list2-title">{{item.imgName}} </text>
						</view>
					</view>
				</view>
			</block>
		</view>
		<text class="loadMore" v-if="!imgList.length">加载中...</text>
	</view>
</template>

<script>
	import NavSwipper from '../../components/jing-swiper.vue'
	import Tag from '../tag/tag.vue'
	let keys = 0;
	export default {
		components: {
			Tag,
			NavSwipper
		},
		data() {
			return {
				refreshing: false,
				lists: [],
				imgList: [],
				fetchPageNum: 1,
				img_root: "https://jp.9188u.com/jp_wallpaper_files/",
				swipperList:[
				'http://jp.9188u.com/jp_wallpaper_files/img/专辑/情侣/15625534967361.jpg',
				'http://jp.9188u.com/jp_wallpaper_files/classify_img/1557126562671.jpg',
				'http://jp.9188u.com/jp_wallpaper_files/img/专辑/古典美人/15571256849831.jpg',
				'http://jp.9188u.com/jp_wallpaper_files/img/专辑/创意多格拼接/15571260899725.jpg',
				'http://jp.9188u.com/jp_wallpaper_files/img/专辑/情侣/15625539376411.jpg',
				'http://jp.9188u.com/jp_wallpaper_files/img/专辑/情侣/15625537095161.jpg',
				]
			}
		},
		onLoad() {
			this.getData();
		},
		onPullDownRefresh() {
			console.log('下拉刷新');
			this.refreshing = true;
			this.getData();
		},
		onReachBottom() {
			console.log('滑动到页面底部')
			this.getData();
		},
		methods: {
			change(e) {
				this.current = e.detail.current;
			},
			getData() {
				uni.request({
					url: "https://jp.9188u.com/getImg?size=5&classify=每日精选&page=" + (this.refreshing ? 1 : this.fetchPageNum),
					success: (res) => {
						let swipper = res.data.list.slice(0,5)
						let ret =[]
						 console.log(res.data.list.slice(0,5))
						 swipper.forEach(item => {
							 ret.push(this.img_root + item.imgPath)
						 })
						 // this.swipperList = ret
						if (res.statusCode !== 200) {
							console.log('请求失败。');
						} else {
							if (this.refreshing && res.data.list[0].id === this.imgList[0].id) {
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
								this.imgList = this.imgList.concat(res.data.list);
								console.log('imglist-----------**************',this.imgList)
								this.fetchPageNum += 1;
							}
						}
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
				uni.navigateTo({
					url: '../detail/detailindex?data=' + encodeURIComponent(JSON.stringify(e))
				})
			}
		}
	}
</script>

<style>
	
	.row {
		display: flex;
		flex-wrap: wrap;
	}
</style>
