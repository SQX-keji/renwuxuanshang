<template>
	<view class="newStepOne">
		<view class="selectBox">
			<view class="select">
				<view class="title">请选择任务类型</view>
				<view v-for="(item, index) in selectOption" :key="index">
					<view class="noExpandItem" v-show="expandId !== item.id" @click="onExpand(item)">
						<view>{{ item.classifyName }}</view>
						<view><tui-icon name="arrowdown"></tui-icon></view>
					</view>
					<view class="expandItem" v-show="expandId === item.id && expand" @click="onNotExpand(item)">
						<view class="sTitle">
							<view>{{ item.classifyName }}</view>
							<view><tui-icon name="arrowup"></tui-icon></view>
						</view>
						<view class="sItem" v-for="(option, index1) in item.list" :key="index1" @click.stop="select(option, item.classifyName)">
							{{ option.classifyDeatilsName }}
						</view>
					</view>
				</view>
			</view>
		</view>
	</view>
</template>

<script>
import tuiIcon from '@/components/thorui/tui-icon/tui-icon.vue';
export default {
	components: {
		tuiIcon
	},
	data() {
		return {
			expandId: 1,
			expand: true,
			selectId: null,
			state:'',
			selectOption: []
		};
	},
	onLoad(params) {
		this.state = params.state;
		this.getselectClassifyList();
	},
	methods: {
		getselectClassifyList() {
			this.$Request.getT('/helpClassify/select').then(res => {
				if (res.code === 0) {
					for (var i = 0; i < res.data.length; i++) {
						this.selectOption.push(res.data[i]);
					}
				}
			});
		},
		/**
		 * 统一跳转接口,拦截未登录路由
		 * navigator标签现在默认没有转场动画，所以用view
		 */
		navToLogin(url) {
			let token = this.$queue.getData('token');
			let mobile = this.$queue.getData('mobile');
			let userId = this.$queue.getData('userId');
			if (token) {
				this.$Request.postJson('/app/selectUserById?userId=' + userId).then(res => {
					if (res.code === 0 && res.data.phone) {
						uni.navigateTo({
							url
						});
					} else {
						uni.navigateTo({
							url: '/pages/public/mobile'
						});
					}
				});
			} else {
				this.goLoginInfo();
			}
		},
		//统一登录跳转
		goLoginInfo() {
			this.$queue.setData('href', '/pages/task/new');
			uni.navigateTo({
				url: '/pages/public/login'
			});
		},
		onExpand: function(item) {
			this.expandId = item.id;
			this.expand = true;
		},
		onNotExpand: function(item) {
			this.expandId = '';
			this.expand = false;
		},
		select: function(item, classifyName) {
			this.selectId = item.id;
			if(this.state){
				this.navToLogin('/pages/task/tasklist?id=' + this.selectId + '&state=click');
			}else{
				this.navToLogin('/pages/task/newTask?id=' + this.selectId + '&name=' + item.classifyDeatilsName + '&classifyName=' + classifyName);
			}
		}
	}
};
</script>

<style lang="less">
.newStepOne {
	background: #ffffff;
	.selectBox {
		background: #ffffff;
		// margin-top: 40rpx;
		padding: 40rpx 36rpx 20rpx 36rpx;
		.select {
			// border-top: 1px solid #F2F2F2;
			border-bottom: 1px solid #f2f2f2;
			.title {
				padding-top: 60rpx;
				padding-bottom: 30rpx;
				font-size: 42rpx;
				font-weight: bold;
			}
			.noExpandItem {
				padding: 40rpx 20rpx;
				font-size: 36rpx;
				font-weight: bold;
				display: flex;
				align-items: center;
				justify-content: space-between;
			}
			.expandItem {
				.sTitle {
					background: #f3f3f3;
					padding: 30rpx 20rpx;
					font-size: 36rpx;
					font-weight: bold;
					display: flex;
					align-items: center;
					justify-content: space-between;
				}
				.sItem {
					font-size: 42rpx;
					height: 128rpx;
					display: flex;
					align-items: center;
					border-bottom: 1px solid #f2f2f2;
					padding: 0 30rpx;
				}
			}
		}
	}
}
</style>
