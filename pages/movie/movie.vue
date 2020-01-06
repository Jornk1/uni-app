<template>
		<view class="page">
			<!-- 视频播放 -->
			<view class="player">
				<video id="myTrailer" :src="trailerInfo.trailer" :poster="trailerInfo.poster" class="movie" controls>
				</video>
			</view>
			
			<!-- 影片基本信息 -->
			<view class="movie-info">
					<image 
					:src="trailerInfo.cover" 
					class="cover"></image>
					<view class="movie-desc">
						
							<view class="title">{{trailerInfo.name}}</view>
							<view class="basic-info">{{trailerInfo.basicInfo}}</view>
							<view class="basic-info">{{trailerInfo.originalName}}</view>
							<view class="basic-info">{{trailerInfo.totalTime}}</view>
							<view class="basic-info">{{trailerInfo.releaseDate}}</view>
							<view class="score-block">
								<view class="big-score">
									<view class="score-words">综合评分：</view>
									<view class="movie-score">{{trailerInfo.score}}</view>
								</view>
								
								<view class="score-stars">
									<block v-if="trailerInfo.score >= 0">
										<trailerStar :innerScore="trailerInfo.score" :isShowInnerScore="false"></trailerStar>
									</block>
									<view class="prise-count">
										{{trailerInfo.prisedCounts}}人点赞
									</view>
								</view>
							</view>
							
					</view>
			</view>
			
			<!-- 分割线 -->
			<view class="line-wapper">
				<view class="line"></view>
			</view>
			
			<!-- 剧情介绍 -->
			<view class="plots-block">
				<view class="plots-title"> 剧情介绍：</view>
				<view class="plots-desc">{{trailerInfo.plotDesc}}</view>
			</view>
			
			<!-- 演职人员 -->
			<view class="scroll-block">
				<view class="plots-title">演职人员</view>
				<scroll-view scroll-x class="scroll-list">
					<view class="actor-wapper" v-for="(item, index) in directorArray" :key="item.staffId">
						<image 
							:src="item.photo"
						  class="single-actor"
							mode="aspectFill"
							@click="lookStarf"
							:data-starffIndex = "index"
						>
						</image>
						<view class="actor-name">{{item.name}}</view>
						<view class="actor-role">{{item.actName}}</view>
					</view>
					<view class="actor-wapper" v-for="(item, index) in actorArray" :key="item.staffId">
						<image 
							:src="item.photo"
						  class="single-actor"
							mode="aspectFill"
							@click="lookStarf"
							:data-starffIndex = "index + directorArray.length"
						></image>
						<view class="actor-name">{{item.name}}</view>
						<view class="actor-role">饰 {{item.actName}}</view>
					</view>
				</scroll-view>
			</view>
		    
			<!-- 剧照 -->
			<view class="scroll-block">
		    	<view class="plots-title">剧照</view>
		    	<scroll-view scroll-x  class="scroll-list">
		    		<image 
		    			mode="aspectFill"  
		    			v-for="(item, index) in plotPicsArray" 
		    			:src="item" 
		    			:key="index"
		    			class="plot-image"
		    			@click="lookMe"
		    			:data-imgIndex = "index"
		    		></image>
		    	</scroll-view>
		    </view>
		</view>
</template>

<script>
	import trailerStar from '../components/trailerStar.vue'
	export default {
		data() {
			return {
              trailerInfo: {},
			  plotPicsArray: [], // 剧照
			  directorArray: [],	// 导演列表
			  actorArray: []		// 演员列表
			}
		},
		onLoad(params) {
			let trailId = params.trailerId
			uni.request({
				url:'https://www.imovietrailer.com/superhero/search/trailer/'+trailId,
				method: "POST",
				data: {
					qq:394904919
				},
				header:{
					'content-type': 'application/x-www-form-urlencoded'
				},
				success: (res) => {
					console.log(res.data)
					if (res.data.status === 200) {
						this.trailerInfo = res.data.data
						this.plotPicsArray = JSON.parse(this.trailerInfo.plotPics)
					}
				}
			})
			// 获取 演职人员
			this.getDirectorAndActor(trailId, 1)
			this.getDirectorAndActor(trailId, 2)
		},
		onReady () { // 相当于vue的 mounted周期
		  // 创建视频上下文对象的  实例
		  this.videoContext = uni.createVideoContext("myTrailer")
		},
		// 页面显示
		onShow() {
			if (this.videoContext) {
				// 视频继续播放
			  this.videoContext.play()
			}
		},
		// 页面隐藏
		onHide() {
			// 视频暂停
			this.videoContext.pause()
		},
		methods:{
			// 获取 导演 和 演员 图片
			getDirectorAndActor (trailerId, role) {
				uni.request({
					url:'https://www.imovietrailer.com/superhero/search/staff/' + trailerId + '/' + role,
					method: "POST",
					data: {
						qq:394904919
					},
					header:{
						'content-type': 'application/x-www-form-urlencoded'
					},
					success: (res) => {
						if (role === 1) {
							this.directorArray = res.data.data
							console.log(this.directorArray)
						} else if (role === 2) {
							this.actorArray = res.data.data
						}
					}
				})
			},
			// 剧照的 大图预览
			lookMe (e) {
				const imgIndex = e.currentTarget.dataset.imgindex
				uni.previewImage({
					current: this.plotPicsArray[imgIndex],
					urls: this.plotPicsArray
				})
			},
			// 演职人员的 大图预览
			lookStarf (e) {
				const imgIndex = e.currentTarget.dataset.starffindex
				let arr = [...this.directorArray, ... this.actorArray]
				let urls = []
				arr.forEach(item => {
					urls.push(item.photo)
				})
				uni.previewImage({
					current: urls[imgIndex],
					urls: urls
				})
			}
		},
		components:{
			trailerStar
		}
	}
</script>

<style>
	@import url("./movie.css");
</style>
