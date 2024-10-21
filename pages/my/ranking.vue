<template>
	<view class="content">
		<view class="view1" style="box-shadow: rgba(183, 183, 183, 0.3) 1px 1px 10px 1px;border-radius: 20upx;display: flex;flex-direction: row;">
			<view style="margin: 100upx 0 0 60upx;height: 100upx; width: 150upx; text-align: center;">
				<view style="font-size: 14px;color: #000000;">排名</view>
				<view style="font-size: 20px;color: #FF580B;">{{ rankNum }}</view>
			</view>
			<view style="margin: 40upx 0 0 50upx;height: 100upx; width: 150upx;text-align: center;">
				<image :src="imageUrl" style="width: 120upx;height: 120upx;"></image>
				<view style="font-size: 14px;color: #999999;margin-top: 10upx;">{{ nickName }}</view>
			</view>
			<view style="margin: 100upx 0 0 50upx;height: 100upx; width: 150upx;text-align: center;">
				<view style="font-size: 14px;color: #000000;">奖金</view>
				<view style="font-size: 20px;color: #FF580B;">¥{{ money }}</view>
			</view>
		</view>

		<view class="view2" style="box-shadow: rgba(183, 183, 183, 0.3) 1px 1px 10px 1px;border-radius: 20upx;">
			<view style="display: flex;flex-direction: row;padding: 20upx;">
				<view style="width: 20%;">排名</view>
				<view style="width: 50%;">用户ID</view>
				<view style="width: 30%;text-align: center;">总收入奖金</view>
			</view>
			<view style="display: flex;flex-direction: row;padding: 20upx;" v-for="(item, index) in rankingList" :key="index">
				<view style="width: 20%;">
					<image v-if="item.rankNum == 1" src="../../static/img/my/number1.png" style="width: 50upx; height: 50upx;"></image>
					<image v-if="item.rankNum == 2" src="../../static/img/my/number2.png" style="width: 50upx; height: 50upx;"></image>
					<image v-if="item.rankNum == 3" src="../../static/img/my/number3.png" style="width: 50upx; height: 50upx;"></image>
					<view v-if="item.rankNum > 3" style="font-size: 19px; color: #999999; margin-left: 15upx;">{{ item.rankNum }}</view>
				</view>
				<view style="width: 50%;display: flex;flex-direction: row;align-items: center;">
					<image :src="item.imageUrl" style="width: 50upx; height: 50upx;"></image>
					<view style="font-size: 14px; color: #333333;margin-left: 20upx; width: 65%;">{{ item.nickName }}</view>
				</view>
				<view style="width: 30%;text-align: center;">
					<view style="font-size: 16px;color: #FF580B;">¥{{ item.money }}</view>
				</view>
			</view>
		</view>
	</view>
</template>

<script>
export default {
	data() {
		return {
			rankingList: [],
			imageUrl: '/static/img/common/logo.jpg',
			money: 0,
			nickName: '游客',
			rankNum: 0
		};
	},
	onLoad() {
		let userId = this.$queue.getData('userId');
		if (userId) {
			this.getRankingList(userId);
		}
	},
	methods: {
		getRankingList(userId) {
			this.$Request.getT('/app/selectRankingList?userId=' + userId).then(res => {
				if (res.code === 0) {
					if (res.data.rankingByUser.rankNum) {
						this.rankNum = res.data.rankingByUser.rankNum;
					} else {
						this.rankNum = '0';
					}
					
					if (res.data.rankingByUser.nickName) {
						this.nickName = res.data.rankingByUser.nickName;
					} else {
						this.nickName = '游客';
					}
					
					if (res.data.rankingByUser.imageUrl) {
						this.imageUrl = res.data.rankingByUser.imageUrl;
					} else {
						this.imageUrl = '/static/img/common/logo.jpg';
					}
					this.money = res.data.rankingByUser.money;
					res.data.rankingList.forEach(d => {
						if(!d.imageUrl){
							d.imageUrl = '/static/img/common/logo.jpg';
						}
						this.rankingList.push(d);
					});
				}
			});
		}
	}
};
</script>

<style scoped>
page{
	background: #FFFFFF;
}
.view1 {
	background-color: #ffffff;
	width: 93%;
	height: 300upx;
	margin-left: 26upx;
	border-radius: 20upx;
	margin-top: 20upx;
}

.view2 {
	background-color: #ffffff;
	width: 93%;
	height: 100%;
	margin-left: 26upx;
	border-radius: 20upx;
	margin-top: 20upx;
}
</style>
