<template>
	<view class="index-content" style="background: white;">
		<!-- #ifdef H5 -->
		<view class="login-view" style="align-items: center;display: flex;flex-direction: row; background:url(../../static/img/my/backgroudred.png); width: 100%; height: 100upx;">
			<!-- #endif -->
			<!-- #ifndef H5 -->
			<view class="login-view" style="padding-top: 50upx;align-items: center;display: flex;flex-direction: row; background:url(../../static/img/my/backgroudred.png); width: 100%; height: 150upx;">
				<!-- #endif -->

				<view class="index-search" style="width: 69%;margin-left: 20upx;">
					<view class="icon_search" @tap="goSearch">
						<text class="cuIcon cuIcon-search" style="margin-right: 12upx"></text>
						<text style="font-size: 24upx;">输入关键字</text>
					</view>
				</view>
				<image src="../../static/img/my/mymessage.png" style="width: 40upx;height: 40upx;margin-left: 40upx;" @tap="goMessage"></image>
				<image src="../../static/img/my/upload.png" style="width: 40upx;height: 40upx;margin-left: 40upx;" @click="goNew('')"></image>
			</view>
			<view class="swiper" v-if="shareList[0]" v-show="shareList[0].value == '是'">
				<swiper class="swiper-container" :autoplay="true" :interval="4000" :circular="true" :indicator-dots="true"
				 indicator-active-color="#e10a07" indicator-color="#cccccc" style="height: 357upx;">
					<block v-for="(item, index3) in banners" :key="index3">
						<swiper-item class="swiper-wrapper" @tap='toNavList(item.url)' v-if="item">
							<image lazy-load="true" fade-show="true" :src="item.imageUrl" style="width: 100%;height: 357upx;" mode="scaleToFill"></image>
						</swiper-item>
					</block>
				</swiper>
			</view>
			<!-- banner结束 -->

			<view class="view1" style="background-color: #FFFFFF;margin-top: 20upx;padding-bottom: 20upx;">
				<view v-if="shareList[1]" v-show="shareList[1].value == '是'" style="display: flex;flex-direction: row;flex-wrap: wrap;">
					<view style="display: flex;flex-direction: row; padding: 10upx;padding-left: 20upx;" v-for="(item, index) in bannerButtonList"
					 :key="index">
						<view style="width: 120upx; height: 100upx;text-align: center;" @tap='toNavList(item.url)' v-if="item">
							<image :src="item.imageUrl" style="width: 80upx;height: 80upx;"></image>
							<view style="font-size: 24upx;color: #000;">{{ item.name }}</view>
						</view>
					</view>
				</view>

				<image v-if="banners3List[0] && shareList[2]&&shareList[2].value == '是'" @tap='toNavList(banners3List[0].url)' :src="banners3List[0].imageUrl"
				 style="width: 100%; height: 160upx;margin-top: 50upx; padding-left: 20upx;padding-right: 20upx;"></image>
				<view v-if="shareList[4]&&shareList[4].value == '是'&&cashList.length>0" style="display: flex;flex-direction: row;padding-bottom: 20upx;;margin-top: 20upx;">
					<image src="../../static/img/index/tongzhi.png" style="width: 30upx; height: 30upx;margin: 20upx 20upx 20upx 20upx;"></image>
					<view class="swiper">
						<swiper class="swiper-container" :circular="true" vertical="false" :autoplay="true" :interval="4000" :duration="1000"
						 indicator-active-color="#e10a07" indicator-color="#cccccc" style="height: 50upx;width: 500upx;">
							<block v-for="(item, index3) in cashList" :key="index3">
								<swiper-item class="swiper-wrapper" v-if="item">
									<view style="align-content: center;font-size: 28upx;color: #999999; margin-top: 18upx;">{{ item.zhifubaoName }}刚刚提现了{{ item.money }}元</view>
								</swiper-item>
							</block>
						</swiper>
					</view>
				</view>
			</view>

			<view v-if="shareList[3]" class="view2" v-show="shareList[3].value == '是'">
				<view style="font-size: 38upx; padding: 30upx 0 0 20upx; color: #000000;">精品服务</view>
				<view style="display: flex;flex-direction: row;flex-wrap: wrap;">
					<view class="view2-view" style="display: flex;flex-direction: row;" v-for="(item, index) in JingPinList" :key="index">
						<image :src="item.imageUrl" class="view2-view2" @tap='toNavList(item.url)'></image>
					</view>
				</view>
			</view>

			<view v-if="shareList[5]" class="view3" v-show="shareList[5].value == '是'">
				<view style="font-size: 38upx; padding: 30upx 0 0 20upx; color: #000000;">推荐任务</view>
				<view style="display: flex;flex-direction: row;flex-wrap: wrap;padding-bottom: 20upx;">
					<view class="view3-view2" style="display: flex;flex-direction: row;" v-for="(item, index) in ClassifyList" :key="index"
					 @tap='ClassifyItem(item)'>
						<view>
							<view style="font-size: 28upx;width: 180upx; color: #333333;">{{ item.classifyName }}</view>
							<view style="display: flex;flex-direction: row;margin-top: 10upx;">
								<view style="font-size: 24upx;color: #999999;">{{ item.describes }}</view>
							</view>
						</view>
						<image :src="item.classifyIcon" style="width: 80upx;height: 80upx;margin-left: 20upx;"></image>
					</view>
					<view class="view3-view2" style="display: flex;flex-direction: row;" @click="goNew('state')">
						<view style="width: 180upx;">
							<view style="font-size: 28upx; color: #333333;">查看更多</view>
							<view style="font-size: 22upx; color: #666666;margin-top: 10upx;">更多信息</view>
						</view>
						<image src="../../static/img/index/more.png" style="width: 80upx;height: 80upx;margin-left: 20upx;"></image>
					</view>
				</view>
			</view>

			<view class="index-coupon has-pd-10" style="margin:10upx 8upx 180upx 8upx ;background: #FFFFFF" v-if="haowuList.length > 0">
				<view style="font-size: 38upx; padding: 4upx 0 16upx 20upx; color: #000000;">精选好物</view>
				<view style="background: #F8F8F8;" class="goods-list" v-if="haowuList.length > 0">
				<orange-goods-list v-for="(item, index18) in haowuList" :tkmoney="item.tkmoney" :tkmoneys="item.tkmoneys"
				 :itemid="item.itemid" :shopname='item.shopname' :isEnable="isEnable" :is-invitation="isInvitation" :logo="logo"
				 :itempic="item.itempic + '_310x310.jpg'" :itemtitle="item.itemtitle" :itemprice="' ¥' + item.itemprice"
				 :itemsale="item.itemsale" :itemendprice="item.itemendprice" :couponmoney="item.couponmoney"></orange-goods-list>
				</view>
			</view>

			<!-- 加载更多提示 -->
			<view class="s-col is-col-24" v-if="haowuList.length > 0">
				<load-more :loadingType="contentText[loadingType]" :contentText="contentText"></load-more>
			</view>
			<!-- 加载更多提示 -->
			<empty v-if="haowuList.length === 0" des="暂无数据" show="false"></empty>
		</view>
</template>

<script>
	export default {
		data() {
			return {
				haowuList: [],
				banners: [],
				taskList: [],
				shareList: [],
				cashList: [],
				page: 1,
				size: 10,
				logo: '../../static/img/mao.png',
				isInvitation: 0,
				JingPinList: [],
				isEnable: "否",
				banners3List: [],
				bannerButtonList: [],
				ClassifyList: [],
				loadingType: 0,
				scrollTop: false,
				contentText: {
					contentdown: '上拉显示更多',
					contentrefresh: '正在加载...',
					contentnomore: '没有更多数据了'
				}
			};
		},
		onShow() {
			let a = this.$queue.getData("isEnable")
			if (a) {
				this.isEnable = a;
			}
			let token = this.$queue.getData('token');
			if (token) {
				this.getSelectBanner(1);
				this.getSelectBanner(2);
				this.getSelectBanner(3);
				this.getSelectBanner(4);
				this.getClassifyList();
				this.getshouyeList();
				this.getCashList();
				this.jingxuanshangpin();
			} else {
				this.goLoginInfo();
			}
		},
		onLoad() {
			let token = this.$queue.getData('token');
			if (token) {
				this.getSelectBanner(1);
				this.getSelectBanner(2);
				this.getSelectBanner(3);
				this.getSelectBanner(4);
				this.getClassifyList();
				this.getshouyeList();
				this.getCashList();
				this.jingxuanshangpin();
			}
			
		},
		methods: {
			jingxuanshangpin(type) {
				this.loadingType = 1;
				this.$Request.getT('/commodity/selectCommodityList?page=' + this.page + '&limit=' + this.size).then(res => {
					if (res.code === 0) {
						if (this.page === 1) {
							this.haowuList = [];
						}
						res.data.list.forEach(d => {
							d.itemsale = d.itemsale > 10000 ? '月销' + (d.itemsale / 10000).toFixed(1) + '万件' : '月销' + d.itemsale + '件';
							this.haowuList.push(d);
						});
						this.loadingType = 0;
					} else {
						this.loadingType = 2;
					}
					if (type === 'Refresh') {
						uni.stopPullDownRefresh(); // 停止刷新
					}
				});
			},
			//好物圈
			haowuquan() {
				this.$Request.getT('/commodity/selectCommodityList?page=1&limit=100').then(res => {
					if (res.code === 0) {
						this.haowuList = [];
						res.data.content.forEach(d => {
							d.itemsale = d.itemsale > 10000 ? '月销' + (d.itemsale / 10000).toFixed(1) + '万件' : '月销' + d.itemsale + '件';
							this.haowuList.push(d);
						});
					}
				});
			},
			ClassifyItem(item) {
				uni.navigateTo({
					url: './classIfyItemTask?id=' + item.id,
				});
			},
			getClassifyList() {
				this.$Request.getT('/helpClassify/selectClassifyList').then(res => {
					if (res.code === 0) {
						this.ClassifyList = [];
						res.data.forEach(d => {
							this.ClassifyList.push(d);
						});
					}
				});
			},
			/**
			 * @param {Object} url
			 */
			toNavList: function(url) {
				if (url.indexOf('/pages/') !== -1) {
					uni.navigateTo({
						url
					});
				} else {
					//#ifndef H5
					uni.navigateTo({
						url: '/pages/member/webview?url=' + url
					});
					//#endif
					//#ifdef H5
					window.location.href = url;
					//#endif
				}
			},
			getCashList() {
				this.cashList = [];
				this.$Request.getT('/cash/selectCashOut').then(res => {
					if (res.code === 0) {
						res.data.forEach(d => {
							this.cashList.push(d);
						});
					}
				});
			},
			getshouyeList() {
				this.$Request.getT('/common/type/condition/shouye').then(res => {
					this.shareList = [];
					if (res.code === 0) {
						res.data.forEach(d => {
							this.shareList.push(d);
						});
					}
				});
			},
			getTaskList() {
				this.$Request.getT('/helpTask/selectRecommendHelpTask').then(res => {
					this.taskList = [];
					if (res.code === 0) {
						res.data.list.forEach(d => {
							this.taskList.push(d);
						});
					}
				});
			},
			goSearch() {
				let token = this.$queue.getData('token');
				if (token) {
					uni.navigateTo({
						url: '../task/search'
					});
				} else {
					this.goLoginInfo();
				}
			},
			//统一登录跳转
			goLoginInfo() {
				this.$queue.setData('href', '/pages/my/index');
				uni.navigateTo({
					url: '/pages/public/login'
				});
			},
			goMessage: function() {
				let token = this.$queue.getData('token');
				if (token) {
					uni.navigateTo({
						url: '/pages/task/renwu'
					});
				} else {
					this.goLoginInfo();
				}
			},
			goNew(state) {
				let token = this.$queue.getData('token');
				if (token) {
					if (state) {
						uni.navigateTo({
							url: '../task/new?state=' + state
						});
					} else {
						uni.navigateTo({
							url: '../task/new'
						});
					}
				} else {
					this.goLoginInfo();
				}
			},
			goTaskItem(id) {
				let token = this.$queue.getData('token');
				if (token) {
					uni.navigateTo({
						url: '../task/taskItem?id=' + id
					});
				} else {
					this.goLoginInfo();
				}
			},
			goTaskIndex() {
				let token = this.$queue.getData('token');
				if (token) {
					uni.switchTab({
						url: '../task/index'
					});
				} else {
					this.goLoginInfo();
				}
			},
			getSelectBanner(index) {
				this.$Request.getT('/banner/selectBannerList?classify=' + index + '&state=1').then(res => {
					if (index === 1) {
						this.banners = [];
					} else if (index === 2) {
						this.bannerButtonList = [];
					} else if (index === 3) {
						this.banners3List = [];
					} else if (index === 4) {
						this.JingPinList = [];
					}

					if (res.code === 0) {
						res.data.forEach(d => {
							if (index === 1) {
								this.banners.push(d);
							} else if (index === 2) {
								this.bannerButtonList.push(d);
							} else if (index === 3) {
								this.banners3List.push(d);
							} else if (index === 4) {
								this.JingPinList.push(d);
							}
						});
					}
				});
			}
		},
		onPageScroll: function(e) {
			this.scrollTop = e.scrollTop > 200;
		},
		onReachBottom: function() {
			this.page = this.page + 1;
			this.jingxuanshangpin('');
		},
		onPullDownRefresh: function() {
			this.page = 1;
			this.jingxuanshangpin('Refresh');
		}
	};
</script>

<style lang="scss">
	@import '../../static/css/index.css';

	.icon_search {
		background: #f6f6f6;
		border-radius: 22px;
		height: 69upx;
		line-height: 36px;
		font-size: 14px;
		color: #dcdcdc;
		text-align: left;
		text-indent: 17px;
		position: relative;
		z-index: 1;
		zoom: 1;
		transition: all 0.4s ease 0s;
		transform-origin: center;
	}

	.view2 {
		background-color: #ffffff;
		width: 100%;
		height: max-content;
		margin-top: 10upx;
		padding: 10upx;
		padding-bottom: 20upx;
	}

	.view3 {
		background-color: #ffffff;
		width: 100%;
		height: fit-content;
		margin-top: 30upx;
		margin-bottom: 20upx;
		padding-bottom: 20upx;
		padding: 10upx;
	}

	.view2-view2 {
		width: 330upx;
		height: 220upx;
		margin: 0upx 0 0 20upx;
		border-radius: 20upx;
		background-size: 100%;
		padding: 30upx 0 0 0upx;
	}

	.view3-view2 {
		width: 330upx;
		height: 170upx;
		margin: 30upx 0 0 30upx;
		border-radius: 20upx;
		background-color: #f5f5f5;
		padding: 35upx 0 0 20upx;
	}
</style>
