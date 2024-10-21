<template>
	<view class="history">
		<view class="task-content" style="margin-top: 20upx; margin-left: 20upx; margin-right: 20upx;">
			<view>
				<uni-list v-show="list.length > 0">
					<uni-list-item :show-arrow="false" v-for="(item, index) in list" :key="index">
						<view style="width: 100%;display: flex;" @click="taskItem(item)">
							<image :src="item.label" style="border-radius: 100upx;width: 100upx;height: 100upx;background-size: 100%;"></image>
							<view style="width:60%;margin-left: 20upx;">
								<view style="width: 80%; flex-wrap: wrap;">{{ item.title }}</view>
								<view style="display: flex; flex-direction: row;">
									<view style="width: 80%;font-size:26upx;margin-right: 30upx;margin-top:10upx;color: #999999;">截止:{{ item.endTime }}</view>
								</view>
							</view>
							<view style="width: 20%;color:#FF332F;font-size: 36upx;font-weight: 600;">
								+{{ item.taskPrice }}元
								<!-- <view style="color: #999999;font-size:26upx;margin-top:5upx;">{{ item.state }}</view> -->
							</view>
						</view>
					</uni-list-item>
				</uni-list>
			</view>
			<!-- 悬浮上拉 -->
			<view class="scroll_top" @tap="topScrollTap" v-bind:class="[scrollTop ? 'active' : '', '']" style="bottom: 56px;"><text class="iconfont icon-shangla"></text></view>

			<!-- 加载更多提示 -->
			<view class="s-col is-col-24" v-if="list.length > 0"><load-more :loadingType="loadingType" :contentText="contentText"></load-more></view>
			<!-- 加载更多提示 -->
			<empty v-if="list.length === 0" des="暂无数据" show="false"></empty>
		</view>
	</view>
</template>

<script>
import uniList from '@/components/uni-list/uni-list.vue';
import uniListItem from '@/components/uni-list-item/uni-list-item.vue';
import tuiListCell from '@/components/list-cell/list-cell';
import tuiLoadmore from '@/components/loadmore/loadmore';
import tuiNomore from '@/components/nomore/nomore';
import tuiTips from '@/components/tips/tips';

export default {
	components: {
		uniList,
		uniListItem,
		tuiListCell,
		tuiLoadmore,
		tuiNomore,
		tuiTips
	},
	data() {
		return {
			list: [],
			rate: [],
			userState: '',
			size: 10,
			page: 1,
			min_id: 1,
			cid: 0,
			type: 1,
			loadingType: 0,
			scrollTop: false,
			contentText: {
				contentdown: '上拉显示更多',
				contentrefresh: '正在加载...',
				contentnomore: '没有更多数据了'
			}
		};
	},
	onPageScroll: function(e) {
		this.scrollTop = e.scrollTop > 200;
	},
	onLoad() {
		let userId = this.$queue.getData('userId');
		this.getUserInfo(userId);
		this.loadList();
	},
	methods: {
		getUserInfo(userId) {
			this.$Request.postJson('/app/selectUserById?userId=' + userId).then(res => {
				if (res.code === 0) {
					this.userState = res.data.member;
				} else {
					uni.showModal({
						showCancel: false,
						title: '登录失败',
						content: res.msg
					});
					this.$queue.logout();
				}
				uni.hideLoading();
			});
		},
		topScrollTap: function() {
			uni.pageScrollTo({
				scrollTop: 0,
				duration: 300
			});
		},
		loadList(type) {
			this.loadingType = 1;
			let userId = this.$queue.getData('userId');
			uni.showLoading({
				title: '加载中...'
			});
			let data = {
				userId: userId,
				page: this.page,
				limit: this.size
			};
			var userStateByMoney = 1;
			this.$Request.getT('/helpTask/selectHelpBrowsingHistoryList', data).then(res => {
				if (res.code === 0) {
					if (this.page === 1 ) {
						this.list = [];
					}
					for (var i = 0; i < res.data.rate.length; i++) {
						if (res.data.rate[i].id === this.userState) {
							userStateByMoney = res.data.rate[i].rate + res.data.rate[0].rate;
						}
						this.rate.push(res.data.rate[i]);
					}
					for (var i = 0; i < res.data.pageUtils.list.length; i++) {
						res.data.pageUtils.list[i].taskPrice =
							res.data.pageUtils.list[i].taskPrice - res.data.pageUtils.list[i].taskPrice * userStateByMoney;
							res.data.pageUtils.list[i].taskPrice = parseFloat(res.data.pageUtils.list[i].taskPrice).toFixed(2)
						this.list.push(res.data.pageUtils.list[i]);
					}
					this.min_id = res.min_id;
					this.loadingType = 0;
				} else {
					this.loadingType = 2;
				}
				uni.hideLoading();
				if (type === 'Refresh') {
					uni.stopPullDownRefresh(); // 停止刷新
				}
			});
		},
		taskItem(item) {
			uni.navigateTo({
				url: './taskItem?id=' + item.id
			});
		},
		onReachBottom: function() {
			this.page = this.page + 1;
			this.loadList();
		},
		onPullDownRefresh: function() {
			this.page = 1;
			this.min_id = 1;
			this.loadList('Refresh');
		}
	}
};
</script>

<style lang="less" scoped>
@import '../../static/less/index.less';
@import '../../static/css/index.css';
page{
	background: #FFFFFF;
	}
</style>
