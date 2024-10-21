<template>
	<view class="myTask">
		<view class="tui-tabs">
			<scroll-view id="tab-bar" scroll-with-animation class="tui-scroll-h" :scroll-x="true" :show-scrollbar="false" style="position: unset;border-bottom: 2upx solid #F8F8F8;">
				<view style="display: flex;">
					<view v-for="(tab, index) in tabBars" :id="tab.id" :data-current="index" @tap="tabClick(tab)">
						<view class="tui-tab-item-title" style="margin-left: 60upx;" :class="{ 'tui-tab-item-title-active': tabIndex == tab.id }">{{ tab.name }}</view>
					</view>
				</view>
			</scroll-view>
		</view>
		<view class="tui-line-h"></view>
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
								</view>
							</view>
							<view style="width: 19%;">
								<view style="color: orange;">+{{ item.taskPrice }}</view>
								<view v-if="item.state === 0" style="width: 150upx;color: #999999;font-size:26upx;margin-top:5upx;">已接单</view>
								<view v-if="item.state === 1" style="color: #999999;font-size:26upx;margin-top:5upx;">待审核</view>
								<view v-if="item.state === 2" style="color: green;font-size:26upx;margin-top:5upx;">已通过</view>
								<view v-if="item.state === 3" style="color: red;font-size:26upx;margin-top:5upx;">已放弃</view>
								<view v-if="item.state === 4" style="color: red;font-size:26upx;margin-top:5upx;">已拒绝</view>
								<view v-if="item.state === 5" style="color: red;font-size:26upx;margin-top:5upx;">维权</view>
								<view v-if="item.state === 6" style="color: red;font-size:26upx;margin-top:5upx;">维权拒绝</view>
								<view v-if="item.state === 7" style="color: red;font-size:26upx;margin-top:5upx;">已超时</view>
							</view>
						</view>
					</uni-list-item>
				</uni-list>
			</view>
			<!-- 悬浮上拉 -->
			<view class="scroll_top" @tap="topScrollTap" v-bind:class="[scrollTop ? 'active' : '','']" style="bottom: 56px;">
				<text class="iconfont icon-shangla"></text>
			</view>
			<!-- 加载更多提示 -->
			<view class="s-col is-col-24" v-if="newsList.length > 0">
				<load-more :loadingType="loadingType" :contentText="contentText"></load-more>
			</view>
			<!-- 加载更多提示 -->
			<empty v-if="newsList.length === 0" des="暂无数据" show="false">
			</empty>
		</view>
	</view>

	</view>

</template>
<script>
	import uniList from '@/components/uni-list/uni-list.vue';
	import uniListItem from '@/components/uni-list-item/uni-list-item.vue';
	import tuiListCell from "@/components/list-cell/list-cell"
	import tuiLoadmore from "@/components/loadmore/loadmore"
	import tuiNomore from "@/components/nomore/nomore"
	import tuiTips from "@/components/tips/tips"

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
				tabIndex: '-1',
				tabBars: [{
						name: '全部',
						id: '-1'
					},
					{
						name: '待审核',
						id: '1'
					},
					{
						name: '已通过',
						id: '2'
					},
					{
						name: '已拒绝',
						id: '3'
					},
					{
						name: '已维权',
						id: '4'
					},
				],
				page: 1,
				cid: 0,
				type: 1,
				size: 10,
				loadingType: 0,
				scrollTop: false,
				contentText: {
					contentdown: "上拉显示更多",
					contentrefresh: "正在加载...",
					contentnomore: "没有更多数据了"
				},
			};
		},
		onShow() {
			this.loadList('', this.tabIndex);
		},
		onLoad() {
			this.loadList('', -1);
		},
		onPageScroll: function(e) {
			this.scrollTop = e.scrollTop > 200;
		},
		methods: {
			topScrollTap: function() {
				uni.pageScrollTo({
					scrollTop: 0,
					duration: 300
				});
			},
			taskItem(item) {
				uni.navigateTo({
					url: './myExamine?id=' + item.id + '&userId=' + item.userId
				});
			},
			loadList: function(type, status) {
				this.loadingType = 1;
				let userId = this.$queue.getData('userId');
				uni.showLoading({
					title: '加载中...'
				});
				let data = {
					"page": this.page,
					"limit": this.size,
					"userId": userId,
					"state": status
				}
				this.$Request.getJ('/helpTask/selectAuditHelpTask', data).then(res => {
					if (res.code === 0) {
						if (this.page === 1 ) {
							this.newsList = [];
						}
						res.data.pageUtils.list.forEach(d =>{
							this.newsList.push(d);
						});
						this.loadingType = 0;
					} else {
						this.loadingType = 2;
					}
					uni.hideLoading();
					if (type === "Refresh") {
						uni.stopPullDownRefresh(); // 停止刷新
					}
				})
			},
			tabClick(tab) {
				this.switchTab(tab.id);
			},
			tabChange(e) {
				this.switchTab(tab.id);
			},
			switchTab(index) {
				this.page = 1;
				this.tabIndex = index;
				this.newsList = [];
				this.loadList("switch", index);
			},
		},
		onReachBottom: function() {
			this.page = this.page + 1;
			this.loadList('', this.tabIndex);
		},
		onPullDownRefresh: function() {
			this.page = 1;
			this.loadList("Refresh", this.tabIndex);
		}
	};
</script>

<style lang="less" scoped>
	@import "../../static/less/index.less";
	@import '../../static/css/index.css';

	page {
		background: #FFFFFF;
	}
</style>
