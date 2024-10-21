<template>
	<view class="myTask">
		<view class="tui-tabs">
			<scroll-view
				id="tab-bar"
				scroll-with-animation
				class="tui-scroll-h"
				:scroll-x="true"
				:show-scrollbar="false"
				style="position: unset;border-bottom: 2upx solid #F8F8F8;"
			>
				<view style="display: flex;">
					<view v-for="(tab, index) in tabBars" :id="tab.id" :data-current="index" @tap="tabClick(tab)">
						<view class="tui-tab-item-title" style="margin-left: 60upx;" :class="{ 'tui-tab-item-title-active': tabIndex == tab.id }">{{ tab.name }}</view>
					</view>
				</view>
			</scroll-view>
		</view>
		<!-- <view class="tui-line-h"></view> -->
		<view class="task-content">
			<view>
				<uni-list v-show="newsList.length > 0">
					<uni-list-item :show-arrow="false" v-for="(item, index) in newsList" :key="index">
						<view style="width: 100%;display: flex;" @click="listItem(item)">
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
								<view class="desc" style="font-size: 24rpx;">{{ item.state }}</view>
								<view v-if="item.state == '已通过'" style="color: orange;" @click.stop="finish(item.id)">结算</view>
							</view>
						</view>
					</uni-list-item>
				</uni-list>
			</view>
			<!-- 悬浮上拉 -->
			<view class="scroll_top" @tap="topScrollTap" v-bind:class="[scrollTop ? 'active' : '', '']" style="bottom: 56px;"><text class="iconfont icon-shangla"></text></view>

			<!-- 加载更多提示 -->
			<view class="s-col is-col-24" v-if="newsList.length > 0"><load-more :loadingType="loadingType" :contentText="contentText"></load-more></view>
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
			tabIndex: '-1',
			tabBars: [
				{
					name: '全部',
					id: '-1'
				},
				{
					name: '待审核',
					id: '0'
				},
				{
					name: '进行中',
					id: '1'
				},
				{
					name: '被拒绝',
					id: '2'
				},
				{
					name: '结算完',
					id: '3'
				}
			],
			page: 1,
			size: 10,
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
	onLoad() {
		this.loadList('', '-1');
	},
	onPageScroll: function(e) {
		this.scrollTop = e.scrollTop > 200;
	},
	methods: {
		finish(id){
			this.$Request.postJson('/helpTask/finishHelpTask?helpTaskId=' + id).then(res =>{
				if(res.code === 0){
					uni.showModal({
					    title: '提示',
					    content: '结算完成',
						showCancel: false,
					});
					
				}else{
					uni.showModal({
					    title: '提示',
					    content: res.msg,
						showCancel: false,
					});
				}
			});
		},
		listItem(item) {
			let token = this.$queue.getData('token');
			if (token) {
				uni.navigateTo({
					url: './myListItem?id=' + item.id
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
		},
		loadList: function(type, status) {
			this.loadingType = 1;
			let userId = this.$queue.getData('userId');
			uni.showLoading({
				title: '加载中...'
			});
			let data = {
				page: this.page,
				limit: this.size,
				state: status,
				userId: userId
			};
			this.$Request.getJ('/helpTask/selectMyHelpTask', data).then(res => {
				if (res.code === 0) {
					if (this.page === 1 ) {
						this.newsList = [];
					}
					for (var i = 0; i < res.data.pageUtils.list.length; i++) {
						this.newsList.push(res.data.pageUtils.list[i]);
						if (res.data.pageUtils.list[i].state === 0) {
							this.newsList[i].state = '待审核';
						} else if (res.data.pageUtils.list[i].state === 1) {
							this.newsList[i].state = '已通过';
						} else if (res.data.pageUtils.list[i].state === 2) {
							this.newsList[i].state = '已拒绝';
						} else if (res.data.pageUtils.list[i].state === 3) {
							this.newsList[i].state = '已结算';
						}
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
			this.loadList('switch', index);
		}
	},
	onReachBottom: function() {
		this.page = this.page + 1;
		this.loadList('', this.tabIndex);
	},
	onPullDownRefresh: function() {
		this.page = 1;
		this.min_id = 1;
		this.loadList('Refresh', this.tabIndex);
	}
};
</script>

<style lang="less">
@import '../../static/less/index.less';
@import '../../static/css/index.css';
</style>
