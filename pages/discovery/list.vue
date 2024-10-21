<template>

	<view class="index-content" style="background: white">
		<scroll-view scroll-x class="nav" scroll-with-animation :scroll-left="scrollLeft" style="position: fixed;z-index: 999;background: white">
			<view class="cu-item" v-bind:key='index' :class="index==TabCur?'text-green cur':''" v-for="(item,index) in category"
			 @tap="tabSelect" :data-id="index">
				<text :style="index==TabCur?'background: #e10a07;color: white;padding: 4px 10px 4px 10px;border-radius: 16px;margin-right: -12px':'background: rgb(220, 220, 220);color: #666666;margin-right: -12px;padding: 4px 10px 4px 10px;border-radius: 16px;'">
					{{item.classifyName}}
				</text>
			</view>
		</scroll-view>


		<view class="index-coupon has-bg-white has-pd-10">
			<view class="goods-list" v-if="handpick.length > 0&&TabCur==0" style="padding-top: 60upx">
				<orange-handpick v-bind:key='index' v-for="(h,index) in handpick" :copy_content="h.copy_content" :total="h.dummy_click_statistics"
				 :content="h.show_content" :images="h.itempic" :showTime="h.show_time" :to="h.itemid"></orange-handpick>
			</view>
		</view>
		<view class="index-coupon has-bg-white has-pd-10 top_30">
			<view class="goods-list" v-if="news.length > 0&&TabCur==1" style="padding-top: 60upx">
				<orange-news v-bind:key='index' v-for="(h,index) in news" :total="h.share_times" :copy_text="h.copy_text" :content="h.show_text"
				 :showTime="h.activity_start_time" :goods="h.goods"></orange-news>
			</view>
		</view>
		<view class="index-coupon has-bg-white has-pd-10 top_30" v-if="TabCur>1">
			<view class="goods-list" v-if="newsList.length > 0&&TabCur>1" style="padding-top: 60upx">
				<orange-mians v-bind:key="index" v-for="(h, index) in newsList" :copy_content="h.content" :total="h.messageId"
				 :content="h.content" :showTime="h.createTime" :to="h.itemid" :images="h.picture" :url="h.articleUrl"></orange-mians>

			</view>
			<view style="padding-top: 400upx;text-align: center;color: #999999;font-size: 28upx;" v-if="newsList.length== 0">暂无数据</view>

		</view>
		<view class="scroll_top" @tap="topScrollTap" v-bind:class="[scrollTop ? 'active' : '','']" style="bottom: 56px;">
			<text class="iconfont icon-shangla"></text>
		</view>
		<!-- 加载更多提示 -->
		<view v-if="TabCur<3">
			<view class="s-col is-col-24" v-if="handpick.length > 0||news.length>0">
				<load-more :loadingType="loadingType" :contentText="contentText"></load-more>
			</view>
		</view>

	</view>

</template>

<script>
	export default {
		name: "Card",
		data() {
			return {
				contentText: {
					contentdown: "上拉显示更多",
					contentrefresh: "正在加载...",
					contentnomore: "没有更多数据了"
				},
				loadingType: 0,
				scrollTop: false,
				TabCur: 0,
				showNews: false,
				showHandPick: true,
				scrollLeft: 0,
				res: [],
				mianDan: [],
				newsList: [],
				handpick: [],
				handpickLoad: {
					loading: false,
					finished: false,
					total: 1,
				},
				category: [],
				news: [],
				classifyId: 1,
				newsLoad: {
					loading: false,
					finished: false,
					total: 1,
				},
				min_id: 1,
				min_id1: 1,
			}
		},
		onLoad: function(e) {
			uni.showLoading({
				title: '加载中...'
			});
			this.getTopList();
			this.getHandpick(1);

			var that = this;
			var timer = setInterval(function() {
				that.min_id = 1;
				that.min_id1 = 1;
				if (that.TabCur == 0) {
					that.getHandpick(1);
				}
				if (that.TabCur == 1) {
					that.getNews(1);
				}
				if (that.TabCur == 2) {
					that.getOrderList();
				}
				if (that.TabCur > 2) {
					that.classifyId = that.category[that.TabCur].id;
					that.getNewsList();
				}
			}, 1000 * 30);

			// this.getNews(1);
		},
		onPageScroll: function(e) {
			this.scrollTop = e.scrollTop > 200;
		},
		onReachBottom: function() {
			if (this.TabCur == 0) {
				this.handpickOnLoad();
			} else {
				this.newsOnLoad();
			}
		},
		onPullDownRefresh: function() {
			this.min_id = 1;
			this.min_id1 = 1;
			if (this.TabCur == 0) {
				this.getHandpick(1);
			}
			if (this.TabCur == 1) {
				this.getNews(1);
			}
			if (this.TabCur == 2) {
				this.getOrderList();
			}
			if (this.TabCur > 2) {
				this.classifyId = this.category[this.TabCur].id;
				this.getNewsList();
			}
		},
		methods: {
			getNewsList() {
				this.$Request.getT('/article/selectArticleList?classifyId=' + this.classifyId).then(res => {
					if (res.code === 0) {
						this.newsList = res.data;
					}
					uni.hideLoading();
					uni.stopPullDownRefresh(); // 停止刷新
				});
			},
			tabSelect(e) {
				uni.hideLoading();
				uni.pageScrollTo({
					scrollTop: 0,
					duration: 500
				});
				this.TabCur = e.currentTarget.dataset.id;
				this.scrollLeft = (e.currentTarget.dataset.id - 1) * 60;
				if (e.currentTarget.dataset.id == 0) {
					if (this.handpick.length == 0) {
						uni.showLoading({
							title: '加载中...'
						});
						this.getHandpick(1);
					} else {
						this.showNews = false;
						this.showHandPick = true;
					}
				}
				if (e.currentTarget.dataset.id == 1) {
					if (this.news.length == 0) {
						uni.showLoading({
							title: '加载中...'
						});
						this.getNews(1);
					} else {
						this.showNews = true;
						this.showHandPick = false;
					}
				}
				if (e.currentTarget.dataset.id == 2) {
					this.getOrderList();
				}
				if (this.TabCur > 2) {
					this.classifyId = this.category[this.TabCur].id;
					this.getNewsList();
				}
			},
			topScrollTap: function() {
				uni.pageScrollTo({
					scrollTop: 0,
					duration: 300
				});
			},
			getOrderList() {
				this.$Request.getT('/freeOfCharge/selectList?page=1&limit=20').then(res => {
					if (res.code === 0) {
						this.mianDan = [];
						for (let i = 0; i < res.data.content.length; i++) {
							res.data.content[i].message = res.data.content[i].message.replace(/↵/g, "\n");
							this.mianDan.push(res.data.content[i])
						}
						// this.mianDan=this.mianDan.reverse();

						// setTimeout(function() {
						// 	uni.pageScrollTo({
						// 		scrollTop: 6000,
						// 		duration: 300
						// 	});
						// }, 1000)

					}
					uni.hideLoading();
					uni.stopPullDownRefresh(); // 停止刷新
				});
			},
			getTopList() {
				this.$Request.getT('/article/selectArticleClassifyList').then(res => {
					if (res.code == 0) {
						this.category = res.data;
					}
				});
			},
			handpickOnLoad() {
				this.getHandpick(this.handpickLoad.total += 1);
			},
			getHandpick(page) { //获取精选商品数据
				this.showNews = false;
				this.showHandPick = true;
				this.loadingType = 1;
				this.$Request.get('/api/selected_item/apikey/maxd/min_id/' + this.min_id).then(res => {
					this.loadingType = 0;
					if (res.code === 1) {
						if (page === 1) {
							this.handpick = [];
						}
						this.min_id = res.min_id;
						for (let i = 0; i < res.data.length; i++) {
							res.data[i].show_content = res.data[i].show_content
								.replace(/&lt;/g, "<")
								.replace(/&gt;/g, ">")
								.replace(/&amp;/g, "&")
								.replace(/&quot;/g, '"')
								.replace(/&apos;/g, "'");
							res.data[i].copy_content = res.data[i].copy_content
								.replace(/&lt;/g, "<")
								.replace(/&gt;/g, ">")
								.replace(/&amp;/g, "&")
								.replace(/&quot;/g, '"')
								.replace(/&apos;/g, "'");
							res.data[i].copy_content = res.data[i].copy_content
								.replace(/<br>/g, "\n");
							this.handpick.push(res.data[i]);
						}
						this.handpickLoad.loading = false;
					} else {
						this.loadingType = 2;
						this.handpickLoad.loading = false;
						this.handpickLoad.finished = true;
					}
					uni.hideLoading();
					uni.stopPullDownRefresh(); // 停止刷新
				})
			},

			getBian(page) { //获取精选商品数据
				this.loadingType = 1;
				this.$Request.get('/api/excellent_editor/apikey/maxd/back/10/min_id/' + this.min_id1).then(res => {
					this.loadingType = 0;
					if (res.code === 1) {
						if (page === 1) {
							this.news = [];
						}
						this.min_id1 = res.min_id;
						for (let i = 0; i < res.data.length; i++) {
							res.data[i].copy_text = res.data[i].copy_text
								.replace(/&lt;/g, "<")
								.replace(/&gt;/g, ">")
								.replace(/&amp;/g, "&")
								.replace(/&quot;/g, '"')
								.replace(/&apos;/g, "'");
							this.news.push(res.data[i]);
						}

						this.newsLoad.loading = false;
					} else {
						this.loadingType = 2;
						this.newsLoad.loading = false;
						this.newsLoad.finished = true;
					}
					uni.hideLoading();
					uni.stopPullDownRefresh(); // 停止刷新
				})
			},
			newsOnLoad() {
				this.getNews(this.newsLoad.total += 1);
			},
			getNews(page) { //获取好货专场数据
				this.loadingType = 1;
				this.showNews = true;
				this.showHandPick = false;
				this.$Request.get('/api/subject_hot/apikey/maxd/min_id/' + this.min_id1).then(res => {
					this.loadingType = 0;
					if (res.code === 1) {
						if (page === 1) {
							this.news = [];
						}
						this.min_id1 = res.min_id;
						for (let i = 0; i < res.data.length; i++) {
							res.data[i].goods = [];
							let itemData = res.data[i].item_data;
							for (let p = 0; p < itemData.length; p++) {
								//有些商品出现无效情况，所以判断...
								if (itemData[p].itemendprice !== undefined) {
									res.data[i].goods.push({
										image: itemData[p].itempic,
										price: '券后价￥' + itemData[p].itemendprice,
										to: itemData[p].itemid,
									});
								}
							}

							res.data[i].show_text = res.data[i].show_text
								.replace(/&lt;/g, "<")
								.replace(/&gt;/g, ">")
								.replace(/&amp;/g, "&")
								.replace(/&quot;/g, '"')
								.replace(/&apos;/g, "'");
							res.data[i].copy_text = res.data[i].copy_text
								.replace(/&lt;/g, "<")
								.replace(/&gt;/g, ">")
								.replace(/&amp;/g, "&")
								.replace(/&quot;/g, '"')
								.replace(/&apos;/g, "'");
							res.data[i].copy_text = res.data[i].copy_text
								.replace(/<br>/g, "\n");
							this.news.push(res.data[i]);
						}
						this.newsLoad.loading = false;
					} else {
						this.loadingType = 2;
						this.newsLoad.loading = false;
						this.newsLoad.finished = true;
					}
					uni.stopPullDownRefresh(); // 停止刷新
					uni.hideLoading();
				})
			},
		}
	}
</script>

<style>
	@import "../../static/css/index.css";

	page {
		background: #FFFFFF;
	}
</style>
