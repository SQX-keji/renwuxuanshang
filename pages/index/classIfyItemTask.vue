<template>
	<view class="task">
			<view class="task-content">
				<view>
					<uni-list v-show="taskList.length > 0">
						<uni-list-item :show-arrow="false" v-for="(item, index) in taskList" :key="index">
							<view style="width: 100%;display: flex;" @click="taskItem(item.id)">
								<image :src="item.label" style="margin-left: 20upx;border-radius: 100upx;width: 100upx;height: 100upx;background-size: 100%;"></image>
								<view style="width:80%;margin-left: 20upx;">
									<view style="display: flex; flex-direction: row;">
										<view style="width: 70%; flex-wrap: wrap;">{{ item.title }}</view>
										<view style="width:30%;margin-left: 10upx;height: 40upx;display: block;text-align: center;background: #e4533d;border-radius: 100upx;">
											<view style="color: #FFFFFF; margin-top: 5upx; font-size: 12px;text-align: center;">做任务</view>
										</view>
									</view>
									<view style="font-size:26upx;margin-top:10upx;display: flex; color: #999999;">
										<image src="../../static/img/task/taskmoney.png" style="margin-top: 10upx;height: 20upx;width: 20upx;"></image>
										<view style="width: 65%;margin-left: 10upx;">+{{ item.taskOriginalPrice }}元</view>
										<view style="margin-left: 10upx; display: flex; flex-direction: row;">
											已完成
											<view style="color: red; margin-top: 5upx;margin-left: 10upx;">{{ item.endNum }}/{{ item.taskNum }}</view>
										</view>
									</view>
								</view>
							</view>
						</uni-list-item>
					</uni-list>
				</view>
				<!-- 悬浮上拉 -->
				<view class="scroll_top" @tap="topScrollTap" v-bind:class="[scrollTop ? 'active' : '', '']" style="bottom: 56px;"><text class="iconfont icon-shangla"></text></view>

				<!-- 加载更多提示 -->
				<view class="s-col is-col-24" v-if="taskList.length > 0"><load-more :loadingType="contentText[loadingType]" :contentText="contentText"></load-more></view>
				<!-- 加载更多提示 -->
				<empty v-if="taskList.length === 0" des="暂无数据" show="false"></empty>
			</view>
	</view>
</template>

<script>
import tuiIcon from '@/components/icon/icon';
import tuiTag from '@/components/tag/tag';
import uniList from '@/components/uni-list/uni-list.vue';
import uniListItem from '@/components/uni-list-item/uni-list-item.vue';
import tuiListCell from '@/components/list-cell/list-cell';
import tuiLoadmore from '@/components/loadmore/loadmore';
import tuiNomore from '@/components/nomore/nomore';
import tuiTips from '@/components/tips/tips';
import tNewsItem from '@/components/views/t-news-item/t-news-item.nvue';
import tRtPopup from '@/components/views/t-rt-popup/t-rt-popup';
import tuiButton from '@/components/thorui/tui-button/tui-button.vue';

export default {
	components: {
		uniList,
		uniListItem,
		tuiIcon,
		tuiTag,
		tuiListCell,
		tuiLoadmore,
		tuiNomore,
		tuiTips,
		tNewsItem,
		tuiButton,
		tRtPopup
	},
	data() {
		return {
			taskList: [],
			rate: [],
			userState: '',
			classifyId:0,
			tabSort: 0,
			page: 1,
			size: 10,
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
	onShow() {
		let userId = this.$queue.getData('userId');
		if (userId) {
			this.getUserInfo(userId);
			this.selectTask('');
		}
	},
	onLoad: function(options) {
		this.classifyId = options.id;
		let userId = this.$queue.getData('userId');
		if (userId) {
			this.getUserInfo(userId);
			this.selectTask('');
		}
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
		selectTask(type) {
			this.loadingType = 1;
			uni.showLoading({
				title: '加载中...'
			});
			var userStateByMoney = 1;
			this.$Request.getJ('/helpTask/selectRecommendHelpTask?page=' + this.page + '&limit=' + this.size + '&id=' + this.classifyId).then(res => {
				if (res.code === 0) {
					if (this.page === 1) {
						this.taskList = [];
					}
					for (var i = 0; i < res.data.rate.length; i++) {
						if (res.data.rate[i].id === this.userState) {
							userStateByMoney = res.data.rate[i].rate + res.data.rate[4].rate;
						}
						this.rate.push(res.data.rate[i]);
					}
					for (var i = 0; i < res.data.pageUtils.list.length; i++) {
						res.data.pageUtils.list[i].taskOriginalPrice =
							res.data.pageUtils.list[i].taskOriginalPrice - res.data.pageUtils.list[i].taskOriginalPrice * userStateByMoney;
						res.data.pageUtils.list[i].taskOriginalPrice = parseFloat(res.data.pageUtils.list[i].taskOriginalPrice).toFixed(2);
						this.taskList.push(res.data.pageUtils.list[i]);
					}
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
		taskItem(id) {
			let token = this.$queue.getData('token');
			if (token) {
				uni.navigateTo({
					url: '../task/taskItem?id=' + id
				});
			} else {
				this.goLoginInfo();
			}
		},
		//统一登录跳转
		goLoginInfo() {
			this.$queue.setData('href', '/pages/task/index');
			uni.navigateTo({
				url: '/pages/public/login'
			});
		},
		topScrollTap: function() {
			uni.pageScrollTo({
				scrollTop: 0,
				duration: 300
			});
		}
	},
	onPageScroll: function(e) {
		this.scrollTop = e.scrollTop > 200;
	},
	onReachBottom: function() {
		this.page = this.page + 1;
		this.selectTask('');
	},
	onPullDownRefresh: function() {
		this.page = 1;
		this.selectTask('Refresh');
	}
};
</script>

<style lang="less" scoped>
@import '../../static/less/index.less';
@import '../../static/css/index.css';
page {
	background-color: #ffffff;
}
</style>
