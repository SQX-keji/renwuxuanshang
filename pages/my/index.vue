<template>
	<view class="content" style="color: #F2F2F2;">
		<view class="view1" style="background:url(../../static/img/my/backgroudred.png); width: 100%; height: 350upx;">
			<view class="login-view" style="display: flex;flex-direction: row;padding-top: 120upx;">
				<image :src="image_url" style="border-radius: 100upx;width: 100upx;height: 100upx;margin-left: 30upx;"></image>
				<view class="login-view-text1" style="margin-left: 30upx;">
					<view style="color: #FFFFFF;">{{ mobile ? mobile : '游客' }}</view>
					<view style="color: #FFFFFF; font-size: 12px;margin-top: 10upx;">邀请码:{{ isInvitation }}</view>
				</view>
				<view class="" style="color: #FFFFFF;margin-left: 30upx; width: 15%;" @tap="invitati()">{{ member }}</view>

				<image src="../../static/img/my/mymessage.png" style="width: 40upx;height: 40upx;margin-left: 18%;" @tap="goMessage"></image>
			</view>
			<view class="order-view">
				<view class="order-view-view" @tap="goMyTask">
					<image src="../../static/img/my/mytask.png" class="order-view-image"></image>
					<view class="order-view-text">我的任务</view>
				</view>
				<view class="order-view-view" @tap="goPrestige">
					<image src="../../static/img/my/myreputation.png" class="order-view-image"></image>
					<view class="order-view-text">我的信誉</view>
				</view>
				<view class="order-view-view" @tap="goRights">
					<image src="../../static/img/my/myright.png" class="order-view-image"></image>
					<view class="order-view-text">我的维权</view>
				</view>
				<view class="order-view-view" @tap="goHistroy">
					<image src="../../static/img/my/historical.png" class="order-view-image"></image>
					<view class="order-view-text">历史浏览</view>
				</view>
			</view>
			
		</view>

		<view class="view2" style="background-color: #FFFFFF; width: 100%; height: 275upx;margin-top: 100upx;padding-top: 10upx;">
			<view @tap="goRanking" class="view2-view" >
				<image src="../../static/img/my/ranking.png" class="view2-view-image"></image>
				<view class="view2-view1">
					<view class="view2-view-text">排行中心</view>
					<image src="../../static/img/my/mygoright.png" class="view2-view-image-right"></image>
				</view>
			</view>
			<view @tap="goWallet" class="view2-view">
				<image src="../../static/img/my/mywallet.png" class="view2-view-image"></image>
				<view class="view2-view1">
					<view class="view2-view-text">我的钱包</view>
					<image src="../../static/img/my/mygoright.png" class="view2-view-image-right"></image>
				</view>
			</view>
			<view class="view2-view" @tap="invitati()">
				<image src="../../static/img/my/myshare.png" class="view2-view-image"></image>
				<view class="view2-view1">
					<view class="view2-view-text">分享好友</view>
					<image src="../../static/img/my/mygoright.png" class="view2-view-image-right"></image>
				</view>
			</view>
		</view>

		<view class="view3" style="background-color: #FFFFFF; width: 100%; height: 200upx;margin-top: 20upx;">
			<view class="view2-view" style="padding-top: 20upx;" @tap="goCustomer()">
				<image src="../../static/img/my/contact.png" class="view2-view-image"></image>
				<view class="view2-view1">
					<view class="view2-view-text">联系客服</view>
					<image src="../../static/img/my/mygoright.png" class="view2-view-image-right"></image>
				</view>
			</view>
			<view @click="toLogout" class="view2-view" style="padding-top: 20upx;">
				<image src="../../static/img/my/outlogin.png" class="view2-view-image"></image>
				<view class="view2-view1">
					<view class="view2-view-text">退出登录</view>
					<image src="../../static/img/my/mygoright.png" class="view2-view-image-right"></image>
				</view>
			</view>
		</view>
	</view>
</template>

<script>
	import tRtPopup from '@/components/views/t-rt-popup/t-rt-popup';
	import tuiButton from '@/components/thorui/tui-button/tui-button.vue';
	export default {
		components: {
			tuiButton,
			tRtPopup
		},
		data() {
			return {
				grade: '',
				phone: '',
				isInvitation: '',
				mobile: '',
				member: '',
				image_url: '/static/logo.png',
				itemList: [{
						title: '发布任务',
						icon: 'new',
						path: 'new'
					},
					{
						title: '我的派单',
						icon: 'my',
						path: 'myList'
					},
					{
						title: '需要审核',
						icon: 'examine',
						path: 'examine'
					},
					{
						title: '派单维权',
						icon: 'rights',
						path: 'rights'
					}
				]
			};
		},
		onLoad() {
			let userId = this.$queue.getData('userId');
			if (userId) {
				this.getUserInfo(userId);
			}
		},
		onShow() {
			let mobile = this.$queue.getData('nickName');
			this.phone = this.$queue.getData('mobile');
			let image_url = this.$queue.getData('image_url');
			let gender = this.$queue.getData('gender');

			if (mobile && mobile !== 'undefined') {
				this.mobile = mobile;
			} else {
				this.mobile = '';
			}
			if (image_url && image_url !== 'undefined') {
				this.image_url = image_url;
			} else {
				this.image_url = '/static/img/common/logo.jpg';
			}
			if (gender) {
				this.gender = gender;
			}

			let userId = this.$queue.getData('userId');
			this.getUserInfo(userId);
		},
		onNavigationBarButtonTap(e) {
			if (e.index === 0) {
				this.$refs.rtBubble.toggle();
			}
		},
		methods: {
			goCustomer() {
				let token = this.$queue.getData('token');
				if (token) {
					uni.navigateTo({
						url: '../member/customer'
					});
				} else {
					this.goLoginInfo();
				}
			},
			rtBubble() {
				let token = this.$queue.getData('token');
				if (token) {
					this.$refs.rtBubble.toggle();
				} else {
					this.goLoginInfo();
				}
			},
			itemClick: function(e) {
				let text = ['new', 'myList', 'examine', 'rights'][e.index];
				uni.navigateTo({
					url: `/pages/task/${text}`
				});
			},
			invitati() {
				uni.navigateTo({
					url: '../invitation/invitationUser'
				});
			},
			//退出登录
			toLogout() {
				let that = this;
				uni.showModal({
					title: '退出提醒',
					content: '确定要退出登录么',
					success: e => {
						if (e.confirm) {
							that.$queue.remove('token');
							that.$queue.remove('userId');
							that.$queue.remove('mobile');
							that.$queue.remove('member');
							that.$queue.remove('openid');
							that.$queue.remove('grade');
							that.$queue.remove('nickName');
							that.$queue.remove('isInvitation');
							that.$queue.remove('image_url');
							that.$queue.remove('relation_id');
							that.$queue.remove('relation');
							that.isShow = false;
							setTimeout(() => {
								uni.showToast({
									icon: 'none',
									title: '登录已退出'
								});
								that.getUserInfo();
							}, 200);
						}
					}
				});
			},
			//获取用户信息
			getUserInfo(userId) {
				this.$Request.postJson('/app/selectUserById?userId=' + userId).then(res => {
					if (res.code === 0) {
						this.$queue.setData('image_url', res.data.imageUrl);
						this.$queue.setData('relation_id', res.data.relationId);
						this.$queue.setData('nickName', res.data.nickName);
						this.$queue.setData('member', res.data.member);
						this.$queue.setData('isInvitation', res.data.invitationCode);
						this.grade = res.data.grade;
						this.isInvitation = res.data.invitationCode;
						this.$queue.setData('relation', res.data.invitationCode);
						this.$queue.setData('gender', parseInt(res.data.gender));
						this.gender = parseInt(res.data.gender);
						if (res.data.imageUrl) {
							this.image_url = res.data.imageUrl;
						} else {
							this.image_url = '/static/img/common/logo.jpg';
						}
						if (res.data.member) {
							if (res.data.member === 1) {
								//未激活
								this.member = '未激活';
							} else if (res.data.member === 2) {
								//未激活
								this.member = '初级会员';
							} else if (res.data.member === 3) {
								//未激活
								this.member = '中级会员';
							} else if (res.data.member === 4) {
								//未激活
								this.member = '高级会员';
							}
						} else {
							this.member = '';
						}
						if (res.data.nickName) {
							this.mobile = res.data.nickName;
						} else {
							this.mobile = res.data.phone;
						}
					} else {
						this.$queue.logout();
						uni.showModal({
							title: '用户信息提示',
							content: '本地用户信息失效需要重新授权登录',
							showCancel: false,
							success: e => {
								uni.navigateTo({
									url: '../public/login'
								});
							}
						});
					}
				});
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
			goWallet: function() {
				let token = this.$queue.getData('token');
				if (token) {
					uni.navigateTo({
						url: '/pages/my/wallet'
					});
				} else {
					this.goLoginInfo();
				}
			},
			goRanking: function() {
				let token = this.$queue.getData('token');
				if (token) {
					uni.navigateTo({
						url: '/pages/my/ranking'
					});
				} else {
					this.goLoginInfo();
				}
			},
			goMyTask: function() {
				let token = this.$queue.getData('token');
				if (token) {
					uni.navigateTo({
						url: '/pages/task/myTask'
					});
				} else {
					this.goLoginInfo();
				}
			},
			goHistroy: function() {
				let token = this.$queue.getData('token');
				if (token) {
					uni.navigateTo({
						url: '/pages/task/history'
					});
				} else {
					this.goLoginInfo();
				}
			},
			goRights: function() {
				let token = this.$queue.getData('token');
				if (token) {
					uni.navigateTo({
						url: '/pages/task/myrights'
					});
				} else {
					this.goLoginInfo();
				}
			},
			goPrestige: function() {
				let token = this.$queue.getData('token');
				if (token) {
					uni.navigateTo({
						url: '/pages/task/prestige'
					});
				} else {
					this.goLoginInfo();
				}
			}
		}
	};
</script>

<style>
	.order-view {
		display: flex;
		flex-direction: row;
		background-color: #ffffff;
		height: 160upx;
		margin-top: 40upx;
		border-top-left-radius: 40upx;
		border-top-right-radius: 40upx;
		padding-left: 30upx;
	}

	.order-view-image {
		width: 50upx;
		height: 50upx;
	}

	.order-view-view {
		text-align: center;
		padding: 40upx 30upx 40upx 10upx;
		width: 180upx;
	}

	.order-view-text {
		color: #000000;
		font-size: 12px;
		margin-top: 10upx;
		margin-left: 8upx;
	}

	.view2-view {
		display: flex;
		flex-direction: row;
		width: 100%;
		height: 85upx;
		align-items: center;
	}

	.view2-view1 {
		display: flex;
		flex-direction: row;
		width: 90%;
		align-items: center;
	}

	.view2-view-image {
		margin-left: 40upx;
		width: 30upx;
		height: 30upx;
	}

	.view2-view-text {
		font-size: 14px;
		color: #000000;
		margin-left: 20upx;
		width: 80%;
	}

	.view2-view-image-right {
		width: 18upx;
		height: 20upx;
		margin-left: 50upx;
	}
</style>
