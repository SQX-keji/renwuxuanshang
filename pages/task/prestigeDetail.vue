<template>
	<view style="text-align: center;">
		<view>
			<view >
				<uni-list v-show="list.length > 0"  class="index-content">
					<uni-list-item class="list-item" :show-arrow="false" v-for="(item, index) in list" :key="index">
						<view class="list-item-wrap">
							<view style="display: flex;justify-content: space-between;">
								<view class="list-title">{{item.title}}</view>
							</view>
							<view style="display: flex;justify-content: space-between;margin-top: 10upx;">
								<view class="list-title">{{item.content}}</view>
							</view>
							<view style="display: flex;justify-content: space-between;margin-top: 10rpx;">
								<view>
									<text>
										{{item.createTime}}
									</text>
									
								</view>
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
			<view class="s-col is-col-24" v-if="list.length > 0">
				<load-more :loadingType="loadingType" :contentText="contentText"></load-more>
			</view>
			<!-- 加载更多提示 -->
			<empty v-if="list.length === 0" des="暂无数据" show="false"></empty>
		</view>


	</view>
</template>

<script>
	import uniListItem from '@/components/uni-list-item/uni-list-item.vue'

	export default {
		components: {
			uniListItem,
		},
		data() {
			return {
				list: [],
				page: 1,
				size : 10,
				min_id: 1,
				cid: 0,
				type: 1,
				isInvitation: 0,
				sort: 4,
				genderKey: "gender",
				loadingType: 0,
				scrollTop: false,
				contentText: {
					contentdown: "上拉显示更多",
					contentrefresh: "正在加载...",
					contentnomore: "没有更多数据了"
				}
			}
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
			loadList: function(type) {
				this.loadingType = 1;
				let userId = this.$queue.getData('userId');
				let data = {
					"page" : this.page,
					"limit" : this.size,
					"userId" : userId
				}
				this.$Request.getJ('/helpTask/selectUserScoreDetails',data).then(res => {
					this.loadingType = 0;
					if (res.code === 0) {
						if (this.page === res.code) {
							this.list = [];
						}
						this.min_id = res.min_id;
						res.data.forEach(d =>{
							this.list.push(d);
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
		},
		onLoad: function(e) {
			this.loadList();
		},
		onReachBottom: function() {
			this.page = this.page + 1;
			this.loadList();
		},
		onPullDownRefresh: function() {
			this.page = 1;
			this.min_id = 1;
			this.loadList("Refresh");
		}
	}
</script>

<style lang="less">
	@import "../../static/less/index.less";
	@import '../../static/css/index.css';
	.index-content {
		background-color: #F3F3F3;
		.list-item {
			background-color: #FFFFFF;
			
			.list-item-wrap {
				display: flex;
				flex-direction: column;

				.list-title {
					font-weight: bold;
				}
			}
		}
	}
</style>
