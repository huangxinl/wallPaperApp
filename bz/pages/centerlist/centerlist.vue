<template>
	<view class="content">
		<view class="cu-list grid col-3 no-border"> 
			<view class="image padding-xs" v-for="(item,index) in imgList" :key="index" @click="goDetail(item)">
				<image class="radius shadow-warp" :src="img_root+item.imgPath" mode="scaleToFill"></image>
				<view class="cuIcon-title bg-yellow text-cut radius">{{item.imgName}}</view>
			</view>
		</view>
	</view>
</template>

<script>
	export default{
		data() {
			return{
				img_root:"https://bz.9188u.com/wallpaper_files/",
				imgList: [],
				title: '专辑列表'
			}
		},
		onLoad (e){
			let data = JSON.parse(decodeURIComponent(e.data));
			console.log(data.id)
			this.getImg(data.id);
			this.title = data.name?(data.name+"-分类列表"):(data.classifyName+"-热门专辑");;
			console.log(data);
			uni.setNavigationBarTitle({
				title: this.title
			});
		},
		methods:{
			goDetail(e) {
				console.log(e);
				uni.navigateTo({
					url: '../detail/detailindex?data=' + encodeURIComponent(JSON.stringify(e))
				})
			},
			getImg(e){
				console.log("开始获取数据");
				uni.request({
					url:"https://bz.9188u.com/getImg?page=1&size=5&classifyId=" + e,
					success: (res) =>{
						console.log(res.data.list);
						this.imgList = res.data.list;
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

<style>
</style>
