<template>
	<view class="content">
		<view class="view1">
			<view style="margin-left: 35%;padding-top: 70upx;font-size: 29px;color: #FFFFFF;">¥ {{ money }}</view>
			<view style="display: flex;flex-direction: row;">
				<view style="width: 30%; margin: 70upx 0 0 50upx;text-align: center;">
					<view style="font-size: 16px;color: #FFFFFF;">可提现金额</view>
					<view style="font-size: 14px;color: #FFFFFF; margin-top: 10upx;">¥{{ mayMoney }}</view>
				</view>
				<view style="background-color: #FFFFFF;width: 1upx; height: 70upx;margin: 70upx 0 0 100upx;"></view>
				<view style="width: 30%; margin: 70upx 0 0 100upx;text-align: center;">
					<view style="font-size: 16px;color: #FFFFFF;">不可提现金额</view>
					<view style="font-size: 14px;color: #FFFFFF; margin-top: 10upx;">¥{{ cannotMoney }}</view>
				</view>
			</view>
		</view>

		<view class="view2">
			<view class="view2-view" @click="goRecharge">
				<image src="../../static/img/my/wallet/recharge.png" class="view2-view-image"></image>
				<view class="view2-view1">
					<view class="view2-view-text">充值</view>
					<image src="../../static/img/my/mygoright.png" class="view2-view-image-right"></image>
				</view>
			</view>
			<view class="view2-view" @tap="goCash">
				<image src="../../static/img/my/wallet/withdrawal.png" class="view2-view-image"></image>
				<view class="view2-view1">
					<view class="view2-view-text">提现</view>
					<image src="../../static/img/my/mygoright.png" class="view2-view-image-right"></image>
				</view>
			</view>
			<view class="view2-view" @tap="detail">
				<image src="../../static/img/my/wallet/moneydetails.png" class="view2-view-image"></image>
				<view class="view2-view1">
					<view class="view2-view-text">资金明细</view>
					<image src="../../static/img/my/mygoright.png" class="view2-view-image-right"></image>
				</view>
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				cannotMoney: 0,
				mayMoney: 0,
				money:0
			};
		},
		onShow() {
			let userId = this.$queue.getData('userId');
			if (userId) {
				this.getUserMoney(userId);
			}
		},
		onLoad() {
			let userId = this.$queue.getData('userId');
			if (userId) {
				this.getUserMoney(userId);
			}
		},
		methods: {
			goCash() {
				uni.navigateTo({
					url: '../member/cash'
				});
			},
			goRecharge() {
				uni.navigateTo({
					url: '../task/recharge'
				});
			},
			detail: function() {
				uni.navigateTo({
					url: './moneydetails'
				});
			},
			getUserMoney(userId) {
				this.$Request.getT('/userMoney/selectUserMoney?userId=' + userId).then(res => {
					if (res.code === 0) {
						this.mayMoney = res.data.mayMoney;
						this.cannotMoney = res.data.cannotMoney;
						this.money = (Number(this.mayMoney)+Number(this.cannotMoney)).toFixed(2);
					}
				});
			},
			getOut() {
				let that = this;
				let token = this.$queue.getData('token');
				let userId = this.$queue.getData('userId');
				if (token) {
					if (that.zhifubao && that.zhifubaoName) {
						if (parseFloat(this.money).toFixed(1) >= 10) {
							uni.showModal({
								title: '提现申请提示',
								content: '请仔细确认收款人信息\n\n姓名:' + that.zhifubaoName + '\n\n收款账号：' + that.zhifubao + '',
								success: e => {
									if (e.confirm) {
										this.$queue.showLoading('提现中...');
										this.$Request.getT('/cash/out/' + userId).then(res => {
											if (res.status === 0 && res.data) {
												that.$queue.showToast('提现申请成功，预计三个工作日到账');
												that.getMoney();
											}
											uni.hideLoading();
										});
									}
								}
							});
						} else {
							this.$queue.showToast('提现金额必须大于或等于10元才可提现');
						}
					} else {
						uni.navigateTo({
							url: '/pages/member/zhifubao'
						});
					}
				} else {
					uni.navigateTo({
						url: '/pages/public/login'
					});
				}
			}
		}
	};
</script>

<style>
	.view1 {
		width: 100%;
		height: 375upx;
		background-image: url(../../static/img/my/wallet/backgroudColor.png);
		background-size: 100%;
	}

	.view2 {
		background-color: #ffffff;
		width: 100%;
		height: 300upx;
		margin-top: 20upx;
	}

	.view2-view {
		display: flex;
		flex-direction: row;
		width: 100%;
		height: 100upx;
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
