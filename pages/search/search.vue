<template>
	<view class="page">
		<view class="search-block">
			<view class="search-icon-wapper">
				<image src="../../static/icos/search.png" class="search-icon"></image>
			</view>
			<input 
				placeholder="搜索预告片" 
				maxlength="10" 
				class="search-text" 
				confirm-type="search"
				@confirm="searchMe"
				/>
		</view>
		<!-- 预告片 -->
		<view class="movie-list page-block">
			<view class="movie-wapper" v-for="item in trailerMovieList" :key="item.id">
					<image 
						:src="item.cover" 
						class="poster"
						:data-trailerId = "item.id"
						@click="toMovieDetail"
					></image>
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
			trailerMovieList:[],
			keywords: '',
			page: 1,
			totalPages: 1 
			};
		},
		onLoad() {
			this.getTrailerMovieList('', 1, 15)
		},
		onReachBottom() {
			let page = this.page + 1
			let keywords = this.keywords
			if (page > this.totalPages) {
				return
			}
			this.getTrailerMovieList(keywords, page, 15)
		},
		methods: {
			getTrailerMovieList(keywords, page, pageSize){
				uni.showLoading({
					mask: true,
					title: "加载中"
				})
				uni.showNavigationBarLoading()
			uni.request({
				url: 'https://www.imovietrailer.com/superhero/search/list',
				method: 'POST',
				data: {
					keywords:keywords,
					qq:394904919,
					page:page,
					pageSize:pageSize
				},
				header: {
					'content-type': 'application/x-www-form-urlencoded'
				},
				success: (res) => {
					console.log(res.data.data);
					if (res.data.status == 200) {
						let list = res.data.data.rows
						this.trailerMovieList = this.trailerMovieList.concat(list)
						this.page = res.data.data.page
						this.totalPages = res.data.data.total
					}
				},
				complete: () => {
					uni.hideLoading()
					uni.hideNavigationBarLoading()
				}
			})
			},
			searchMe (e) {
				let value = e.detail.value
				this.keywords = value 
				this.trailerMovieList = []
				this.getTrailerMovieList(this.keywords, 1, 15)
			},
			// 去详情页面
			toMovieDetail (e) {
				let trailerId = e.currentTarget.dataset.trailerid
				uni.navigateTo({
					url: "../movie/movie?trailerId=" + trailerId
				})
			}
	    },
	}
</script>

<style>
@import url("./search.css");
</style>
