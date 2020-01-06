<template>
	<view class="page">
		<!-- 轮播图 -->
		<swiper :indicator-dots="true" :autoplay="true" :interval="3000" :duration="1000" class="carousel">
			<swiper-item v-for="item in carouselList" :key="item.movieId">
				<navigator open-type="navigate" :url="'../movie/movie?trailerId=' + item.movieId">
					<image :src="item.image" class="carousel"></image>
				</navigator>
			</swiper-item>
			<!-- <swiper-item>
				<image src="../../static/carousel/batmanvssuperman.png" class="carousel"></image>
			</swiper-item>
			<swiper-item>
				<image src="../../static/carousel/spiderman.png" class="carousel"></image>
			</swiper-item> -->
		</swiper>
		<!-- 热门超英 start -->
		<view class="page-block super-hot">
			<view class="hot-title-wapper">
				<image src="../../static/icos/hot.png" class="hot-ico"></image>
				<view class="hot-title">
					热门超英
				</view>
			</view>
		</view>
		<!-- 横向滑动 -->
		<scroll-view scroll-x="true" class="page-block hot">

			<view class="single-poster" v-for="superhero in hotSuperheroList">
				<view class="poster-wapper">
					<navigator open-type="navigate" :url="'../movie/movie?trailerId=' + superhero.id">
						<image :src="superhero.cover" class="poster"></image>
					</navigator>
					<view class="movie-name">
						{{superhero.name}}
					</view>
					<trailerStars :innerScore="superhero.score" showNum="1"></trailerStars>
				</view>
			</view>
		</scroll-view>

		<!-- 热门预告 start -->
		<view class="page-block super-hot">
			<view class="hot-title-wapper">
				<image src="../../static/icos/interest.png" class="hot-ico"></image>
				<view class="hot-title">
					热门预告
				</view>
			</view>
		</view>
		<!-- 热门影视预告视频 -->
		<view class="hot-movies page-block">
			<video :id="trailer.id" :data-playingindex="trailer.id" @play="meIsPlaying" v-for="trailer in hotTrailerList" :src="trailer.trailer"
			 :poster="trailer.poster" class="hot-movie-single" controls></video>
		</view>
		<!-- 猜你喜欢 start -->
		<view class="page-block super-hot">
			<view class="hot-title-wapper">
				<image src="../../static/icos/guess-u-like.png" class="hot-ico"></image>
				<view class="hot-title">
					猜你喜欢
				</view>
			</view>
		</view>
		<view class="page-block guess-u-like">
			<view class="single-like-movie" v-for="(guess,gIndex) in guessULikeList">

				<navigator open-type="navigate" :url="'../movie/movie?trailerId=' + guess.id">
					<image :src="guess.cover" class="like-movie"></image>
				</navigator>

				<view class="movie-desc">
					<view class="movie-title">
						{{guess.name}}
					</view>
					<trailerStars :innerScore="9.1" showNum="0"></trailerStars>
					<view class="movie-info">
						{{guess.basicInfo}}
					</view>
					<view class="movie-info">
						{{guess.releaseDate}}
					</view>
				</view>

				<view class="movie-oper" :data-gIndex="gIndex" @click="praiseMe">
					<image src="../../static/icos/praise.png" class="praise-ico"></image>
					<view class="praise-me">
						点赞
					</view>
					<view :animation="animationDataArr[gIndex]" class="praise-me animation-opacity">
						+1
					</view>
				</view>
			</view>
		</view>
	</view>
</template>

<script>
	import trailerStars from '../components/trailerStar.vue'
	export default {
		data() {
			return {
				carouselList: [],
				hotSuperheroList: [],
				hotTrailerList: [],
				guessULikeList: [],
				animationData: {},
				animationDataArr: [{}, {}, {}, {}, {}]
			}
		},
		onUnload() {
			// 页面卸载的时候，清除动画数据
			this.animationData = {};
			this.animationDataArr = [{}, {}, {}, {}, {}];
		},
		onPullDownRefresh() {
			this.refresh();
		},
		onHide() {
			if (this.videoContext) {
				this.videoContext.pause();
			}
		},
		onLoad() {
			// 在页面创建的时候，创建一个临时动画对象
			this.animation = uni.createAnimation();
			// 轮播图图片
			uni.request({
				url: 'https://www.imovietrailer.com/superhero/index/carousel/list',
				method: 'POST',
				data: {
					qq: 394904919
				},
				header: {
					'content-type': 'application/x-www-form-urlencoded'
				},
				success: (res) => {
					console.log(res.data);
					if (res.data.status == 200) {
						var carouselList = res.data.data;
						this.carouselList = carouselList;
					}
				}
			});
			// 查询热门超英
			uni.request({
				url: 'https://www.imovietrailer.com/superhero/index/movie/hot?type=superhero',
				method: "POST",
				header: {
					'content-type': 'application/x-www-form-urlencoded'
				},
				data: {
					qq: '394904919'
				},
				success: (res) => {
					console.log(res.data);
					// 获取真实数据之前，务必判断状态是否为200
					if (res.data.status == 200) {
						var hotSuperheroList = res.data.data;
						this.hotSuperheroList = hotSuperheroList;
					}

				}
			});
			// 查询热门预告
			uni.request({
				url: 'https://www.imovietrailer.com/superhero/index/movie/hot?type=trailer',
				method: "POST",
				header: {
					'content-type': 'application/x-www-form-urlencoded'
				},
				data: {
					qq: '394904919'
				},
				success: (res) => {
					console.log(res.data);
					// 获取真实数据之前，务必判断状态是否为200
					if (res.data.status == 200) {
						var hotTrailerList = res.data.data;
						this.hotTrailerList = hotTrailerList;
					}

				},
			});
			this.refresh();
		},
		methods: {
			// 播放一个视频的时候，需要暂停其他正在播放的视频
			meIsPlaying(e) {
				var me = this;
				var trailerId = "";
				if (e) {
					trailerId = e.currentTarget.dataset.playingindex;
					me.videoContext = uni.createVideoContext(trailerId);
				}
				var hotTrailerList = me.hotTrailerList;
				for (var i = 0; i < hotTrailerList.length; i++) {
					var tempId = hotTrailerList[i].id;
					if (tempId != trailerId) {
						uni.createVideoContext(tempId).pause();
					}
				}
			},
			refresh() {
				uni.showLoading({
					mask: true
				});
				uni.showNavigationBarLoading();
				// 查询猜你喜欢数据列表
				uni.request({
					url: 'https://www.imovietrailer.com/superhero/index/guessULike',
					method: "POST",
					header: {
						'content-type': 'application/x-www-form-urlencoded'
					},
					data: {
						qq: '394904919'
					},
					success: (res) => {
						// 获取真实数据之前，务必判断状态是否为200
						if (res.data.status == 200) {
							var guessULikeList = res.data.data;
							this.guessULikeList = guessULikeList;
						}
					},
					complete: () => {
						uni.hideNavigationBarLoading();
						uni.hideLoading();
						uni.stopPullDownRefresh();
					}
				});
			},
			// 实现点赞动画效果
			praiseMe(e) {
				var gIndex = e.currentTarget.dataset.gindex;
				console.log(gIndex);
				return;
				// 构建动画数据，并且通过step来表示这组动画的完成
				this.animation.translateY(-60).opacity(1).step({
					duration: 400
				});

				// 导出动画数据到view组件，实现组件的动画效果
				// this.animationData = this.animation.export();
				this.animationData = this.animation;
				this.animationDataArr[gIndex] = this.animationData.export();

				// 还原动画
				setTimeout(function() {
					this.animation.translateY(0).opacity(0).step({
						duration: 0
					});
					this.animationData = this.animation;
					this.animationDataArr[gIndex] = this.animationData.export();
				}.bind(this), 500)
			}
		},
		components: {
			trailerStars
		}
	}
</script>

<style>
	@import url("index.css");
</style>
