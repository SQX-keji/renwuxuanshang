<template>
	<!-- 我的维权  -->
	<view class="myTask">
		<view class="tui-tabs">
			<scroll-view id="tab-bar" scroll-with-animation class="tui-scroll-h" :scroll-x="true" :show-scrollbar="false" style="position: unset;border-bottom: 2upx solid #F8F8F8;">
				<view style="display: flex;">
					<view v-for="(tab, index) in tabBars" :id="tab.id" :data-current="index" @tap="tabClick(tab)">
						<view class="tui-tab-item-title" style="margin-left: 200upx;" :class="{ 'tui-tab-item-title-active': tabIndex == tab.id }">{{ tab.name }}</view>
					</view>
				</view>
			</scroll-view>
		</view>
		<view class="task-content">
			<view>
				<uni-list v-show="newsList.length > 0">
					<uni-list-item :show-arrow="false" v-for="(item, index) in newsList" :key="index">
						<view class="list-item-wrap" @click="taskItem(item)">
							<image :src="item.label" style="border-radius: 100upx;width: 100upx;height: 100upx;background-size: 100%;"></image>
							<view style="width:60%;margin-left: 20upx;">
								<view>{{ item.title }}</view>
								<view style="font-size:26upx;margin-right: 30upx;margin-top:10upx">
									ID:{{ item.id }}
									<!-- <text style="margin-left: 100upx;">{{ item.endNum }}人已接</text> -->
								</view>
							</view>
							<view>
								<view style="color: orange;">+{{ item.taskPrice }}</view>
								<view v-if="item.state == '待审核'" class="desc" style="font-size: 24rpx;">{{ item.state }}</view>
								<view v-if="item.state == '失败'" class="desc" style="font-size: 24rpx;color:red">{{ item.state }}</view>
								<view v-if="item.state == '获胜'" class="desc" style="font-size: 24rpx;color:green">{{ item.state }}</view>
							</view>
						</view>
					</uni-list-item>
				</uni-list>
			</view>
			<!-- 悬浮上拉 -->
			<view class="scroll_top" @tap="topScrollTap" v-bind:class="[scrollTop ? 'active' : '', '']" style="bottom: 56px;"><text
				 class="iconfont icon-shangla"></text></view>

			<!-- 加载更多提示 -->
			<view class="s-col is-col-24" v-if="newsList.length > 0">
				<load-more :loadingType="loadingType" :contentText="contentText"></load-more>
			</view>
			<!-- 加载更多提示 -->
			<empty v-if="newsList.length === 0" des="暂无数据" show="false"></empty>
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
				newsList: [],
				tabIndex: 'hot',
				tabBars: [{
						name: '维权中',
						id: 'hot',
						state: '0'
					},
					{
						name: '已完成',
						id: 'yule',
						state: '1'
					}
				],
				page: 1,
				size: 10,
				cid: 0,
				tabState: '0',
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
		onLoad() {
			this.loadList('', 0);
		},
		onPageScroll: function(e) {
			this.scrollTop = e.scrollTop > 200;
		},
		methods: {
			loadList: function(type, status) {
				this.loadingType = 1;
				let userId = this.$queue.getData('userId');
				uni.showLoading({
					title: '加载中...'
				});
				let data = {
					page: this.page,
					limit: this.size,
					userId: userId,
					state: status
				};
				this.$Request.getT('/helpTask/selectHelpMaintainListBySendOrder', data).then(res => {
					if (res.code === 0) {
						if (this.page === 1 ) {
							this.newsList = [];
						}
						for (var i = 0; i < res.data.list.length; i++) {
							this.newsList.push(res.data.list[i]);
							if (res.data.list[i].state === 0) {
								this.newsList[i].state = '待审核';
							} else if (res.data.list[i].state === 1) {
								//this.newsList[i].state = '接单人获胜';
								this.newsList[i].state = '获胜';
							} else if (res.data.list[i].state === 2) {
								//this.newsList[i].state = '派单人获胜';
								this.newsList[i].state = '失败';
							}
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
			topScrollTap: function() {
				uni.pageScrollTo({
					scrollTop: 0,
					duration: 300
				});
			},
			taskItem(item) {
				let token = this.$queue.getData('token');
				if (token) {
					if (item.state === '待审核') {
						uni.showModal({
							title: '平台回复',
							content: '平台正在处理中，请稍后。。。',
							showCancel: false,
						});
					} else {
						uni.showModal({
							title: '平台回复',
							content: item.content,
							showCancel: false,
						});
					}
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
			tabClick(tab) {
				this.switchTab(tab.id, tab.state);
			},
			tabChange(e) {
				this.switchTab(tab.id, tab.state);
			},
			switchTab(index, state) {
				this.tabState = state;
				this.page = 1;
				this.tabIndex = index;
				this.newsList = [];
				this.loadList('switch', state);
			}
		},
		onReachBottom: function() {
			this.page = this.page + 1;
			this.loadList('', this.tabState);
		},
		onPullDownRefresh: function() {
			this.page = 1;
			this.loadList('Refresh', this.tabState);
		}
	};
</script>

<style lang="less">
	@import '../../static/less/index.less';
	@import '../../static/css/index.css';
	page{
		background: #FFFFFF;
	}
</style>
