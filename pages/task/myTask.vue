<template>
	<!-- 我的任务 -->
	<view class="myTask">
		<view class="tui-tabs">
			<scroll-view id="tab-bar" scroll-with-animation class="tui-scroll-h" :scroll-x="true" :show-scrollbar="false" style="position: unset;border-bottom: 2upx solid #F8F8F8;">
				<view style="display: flex;">
					<view :key='index' v-for="(tab, index) in tabBars" :id="tab.id" :data-current="index" @tap="tabClick(tab)">
						<view class="tui-tab-item-title" style="margin-left: 70upx;" :class="{ 'tui-tab-item-title-active': tabIndex == tab.id }">{{ tab.name }}</view>
					</view>
				</view>
			</scroll-view>
		</view>

		<view class="task-content" style="margin-top: 20upx; margin-left: 20upx; margin-right: 20upx;">
			<view>
				<uni-list v-show="newsList.length > 0">
					<uni-list-item :show-arrow="false" v-for="(item, index) in newsList" :key="index">
						<view style="box-shadow: rgba(183, 183, 183, 0.3) 1px 1px 10px 1px;width: 100%;display: flex;" @click="taskItem(item)">
							<image :src="item.label" style="border-radius: 100upx;width: 100upx;height: 100upx;background-size: 100%;"></image>
							<view style="width:60%;margin-left: 20upx;">
								<view style="width: 80%; flex-wrap: wrap;">{{ item.title }}</view>
								<view style="display: flex; flex-direction: row;">
									<view style="width: 80%;font-size:26upx;margin-right: 30upx;margin-top:10upx;color: #999999;">截止:{{ item.endTime }}</view>
								</view>
							</view>
							<view style="width: 20%;color:#FF332F;font-size: 32upx;font-weight: 600;">
								<view v-if="item.state != '已放弃' && item.state != '已拒绝' && item.state != '维权拒绝'  && item.state != '已超时'">+{{ item.taskPrice }}元</view>
								<view style="color: red;font-size:26upx;margin-top:10upx;">{{ item.state }}</view>
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
						name: '全部',
						id: 'hot',
						state: '-1'
					},
					{
						name: '待审核',
						id: 'yule',
						state: '1'
					},
					{
						name: '已通过',
						id: 'sports',
						state: '2'
					},
					{
						name: '未通过',
						id: 'domestic',
						state: '3'
					},
					{
						name: '维权',
						id: 'finance',
						state: '4'
					}
				],
				page: 1,
				size: 10,
				min_id: 1,
				tabState: '-1',
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
			this.loadList(0, '-1');
		},
		onLoad() {
			this.loadList(0, '-1');
		},
		onPageScroll: function(e) {
			this.scrollTop = e.scrollTop > 200;
		},
		topScrollTap: function() {
			uni.pageScrollTo({
				scrollTop: 0,
				duration: 300
			});
		},
		methods: {
			loadList: function(type, status) {
				this.loadingType = 1;
				uni.showLoading({
					title: '加载中...'
				});
				let userId = this.$queue.getData('userId');
				let data = {
					page: this.page,
					limit: this.size,
					state: status,
					userId: userId
				};
				this.$Request.getT('/helpTask/selectParticipationHelpTask', data).then(res => {
					if (res.code === 0) {
						if (this.page === 1 ) {
							this.newsList = [];
						}
						this.min_id = res.min_id;
						for (var i = 0; i < res.data.list.length; i++) {
							this.newsList.push(res.data.list[i]);
							//0接单成功 1提交待审核 2审核成功 3拒绝 4维权 5延期  6维权拒绝
							if (res.data.list[i].state === 0) {
								this.newsList[i].state = '已接单';
							} else if (res.data.list[i].state === 1) {
								this.newsList[i].state = '已提交';
							} else if (res.data.list[i].state === 2) {
								this.newsList[i].state = '已通过';
							} else if (res.data.list[i].state === 3) {
								this.newsList[i].state = '已拒绝';
							} else if (res.data.list[i].state === 4) {
								this.newsList[i].state = '已维权';
							} else if (res.data.list[i].state === 5) {
								this.newsList[i].state = '已放弃';
							} else if (res.data.list[i].state === 6) {
								this.newsList[i].state = '维权拒绝';
							} else if (res.data.list[i].state === 7) {
								this.newsList[i].state = '已超时';
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
			taskItem(item) {
				if (item.state === '已拒绝' || item.state === '已维权') {
					uni.navigateTo({
						url: './rightListItem?id=' + item.id
					});
				} else {
					uni.navigateTo({
						url: './taskItem?id=' + item.id
					});
				}
			},
			tabClick(tab) {
				this.switchTab(tab.id, tab.state);
			},
			tabChange(e) {
				this.switchTab(tab.id, tab.state);
			},
			switchTab(index, state) {
				this.page = 1;
				this.tabState = state;
				this.tabIndex = index;
				this.newsList = [];
				this.loadList('switch', this.tabState);
			}
		},
		onReachBottom: function() {
			this.page = this.page + 1;
			this.loadList('', this.tabState);
		},
		onPullDownRefresh: function() {
			this.page = 1;
			this.min_id = 1;
			this.loadList('Refresh', this.tabState);
		}
	};
</script>

<style lang="less" scoped>
	@import '../../static/less/index.less';
	@import '../../static/css/index.css';

	page {
		background: #FFFFFF;
	}

	.day {
		width: 110upx;
		height: 30upx;
		margin-top: 10upx;
		background-color: pink;
		color: red;
		font-size: 22upx;
		text-align: center;
		line-height: 30upx;
		border-radius: 10upx;
	}
</style>
