<template>
	<view class="task">
		<!-- 搜索板块 -->
		<view class="index-header">
			<view class="">
				<!-- 搜索板块 -->
				<view class="tui-header">
					<view class="tui-search" style="height: 60upx; width: 500upx;">
						<input type="text" class="text-search" v-model="search" style="line-height: 30upx; padding-top: 10upx;" placeholder="请输入任务名称" />
					</view>
					<view @tap='rtBubble' style="color: #333333; background-color: #FFFFFF; font-size: 15px;">搜索</view>
					<view></view>
				</view>
			</view>
		</view>
		<view class="listitem" v-for="(item, index) in selectOption" :key="index">
			<view class="title" style="padding: 20upx;">
				<view class="item-text-title" style="margin-left: 20upx;margin: 10upx; color: #333;">{{ item.classifyName }}</view>
				<view class="item">
					<view v-for="(option, index1) in item.list" :key="index1" @click="itemClick(option)">
						<view class="item-text">{{ option.classifyDeatilsName }}</view>
					</view>
				</view>
			</view>
		</view>
	</view>
</template>

<script>
export default {
	components: {},
	data() {
		return {
			selectOption: [],
			search: ''
		};
	},
	onLoad() {
		this.getselectClassifyList1();
	},
	methods: {
		getselectClassifyList1() {
			this.$Request.getT('/helpClassify/select').then(res => {
				if (res.code === 0) {
					for (var i = 0; i < res.data.length; i++) {
						this.selectOption.push(res.data[i]);
					}
				}
			});
		},
		navBack() {
			uni.navigateBack();
		},
		rtBubble() {
			if (this.search === '') {
				uni.showToast({
					icon: 'none',
					title: '搜索框输入为空'
				});
				return;
			}
			uni.navigateTo({
				url: './tasklist?title=' + this.search + '&state=' + '123'
			});
		},
		itemClick(option) {
			uni.navigateTo({
				url: './tasklist?id=' + option.id + '&state=' + 'click'
			});
		}
	}
};
</script>

<style lang="less" scoped>
@import '../../static/less/index.less';
@import '../../static/css/index.css';

page{
	background-color: #FFFFFF;
}

.text-search {
	margin-left: 20upx;
	margin-top: 5upx;
	font-size: 14px;
	color: #000000;
}

.item-text-title {
	color: #000000;
	font-size: 15px;
	font-weight: bold;
}

.item {
	display: flex;
	flex-direction: row;
	flex-wrap: wrap;
}

.item-text {
	margin-left: 20upx;
	border-radius: 50upx;
	margin: 10upx;
	border: 1px solid #dee0de;
	padding: 5upx;
	padding-left: 20upx;
	padding-right: 20upx;
	font-size: 13px;
	color: #555555;
}
</style>
