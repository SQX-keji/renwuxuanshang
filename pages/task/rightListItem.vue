<template>
	<view class="newTask">
		<!-- 第一部分 -->
		<view style="box-shadow: rgba(183, 183, 183, 0.3) 1px 1px 10px 1px;border-radius: 20upx;display: flex; flex-direction: column;background-color: #fff;padding: 20upx 0upx 30upx 20upx; margin: 20upx 20upx 20upx 20upx;">
			<view style="width: 95%;display: flex;">
				<image :src="list.helpTask.label" style="margin-left: 20upx;border-radius: 100upx;width: 100upx;height: 100upx;background-size: 100%;"></image>
				<view style="width:60%;margin-left: 20upx;">
					<view style="color: #000000; font-weight: bold;">{{ list.helpTask.title }}</view>
					<view style="font-size:26upx;margin-right: 30upx;margin-top:10upx;color: #666666;">ID:{{ list.helpTask.id }}</view>
				</view>
				<view style="width: 20%;color:#ff5300;font-size: 36upx;font-weight: 600;">¥{{ list.helpTask.taskOriginalPrice }}</view>
			</view>
			<view style="display: flex;flex-direction: row;padding-top: 30upx;">
				<view class="titile2-text" style="width: 80%; color: #666666;">发布时间 {{ list.helpTask.createTime }}</view>
				<view v-if="flag === 3" style="width: 150upx;color: #999999;font-size:26upx;margin-top:5upx;">待审核</view>
				<view v-if="flag === 2" style="color: #999999;font-size:26upx;margin-top:5upx;">已接单</view>
				<view v-if="flag === 4" style="color: green;font-size:26upx;margin-top:5upx;">已通过</view>
				<view v-if="flag === 7" style="color: red;font-size:26upx;margin-top:5upx;">已放弃</view>
				<view v-if="flag === 5" style="color: red;font-size:26upx;margin-top:5upx;">已拒绝</view>
				<view v-if="flag === 6" style="color: red;font-size:26upx;margin-top:5upx;">维权</view>
				<view v-if="flag === 8" style="color: red;font-size:26upx;margin-top:5upx;">维权拒绝</view>
				<view v-if="flag === 9" style="color: red;font-size:26upx;margin-top:5upx;">超时</view>
			</view>
		</view>
		<!-- 第二部分 -->
		<view class="titile2" style="box-shadow: rgba(183, 183, 183, 0.3) 1px 1px 10px 1px;border-radius: 20upx;margin: 0upx 20upx 20upx 20upx; padding: 0upx 20upx 20upx 20upx; background: #FFFFFF;">
			<view style="color: #000000; font-weight: bold; font-size: 18px; padding: 20upx 0 10upx 10upx;">任务详情</view>
			<view class="titile2-group">
				<view class="titile2-text">任务数量</view>
				<view class="titile2-text1">{{ list.helpTask.endNum }}人已做，还剩{{ list.helpTask.taskNum - list.helpTask.endNum }}个</view>
			</view>
			<view class="titile2-group">
				<view class="titile2-text">任务限时</view>
				<view class="titile2-text1">{{ list.helpTask.restrictTime }}</view>
			</view>

			<view class="titile2-group">
				<view class="titile2-text">限时审核</view>
				<view class="titile2-text1">{{ list.helpTask.auditTime }}</view>
			</view>
			<view class="titile2-group">
				<view class="titile2-text">说明</view>
				<view class="titile2-text1" style="margin-left: 90upx; width: 70%;">{{ list.helpTask.content }}</view>
			</view>
		</view>
		<!-- 第三部分 -->
		<view style="box-shadow: rgba(183, 183, 183, 0.3) 1px 1px 10px 1px;border-radius: 20upx;margin: 0upx 20upx 20upx 20upx; padding: 0upx 20upx 20upx 20upx; background: #FFFFFF;">
			<!-- type:1链接 2口令 3扫码 -->
			<view class="type1" v-if="list.helpTask.openType == 1">
				<view class="titile2-group">
					<view class="titile-text" style="width: 100upx;">链接</view>
					<view class="titile2-text3">{{ list.helpTask.openContent }}</view>
				</view>
				<view class="titile2-group2" style="background-color: #FFFFFF;"><button class="confirm-btn" @click="toNavList(list.helpTask.openContent)">直达链接</button></view>
			</view>
			<view class="type2" v-if="list.helpTask.openType == 2">
				<view class="titile2-group">
					<view class="titile-text" style="width: 100px;">复制口令</view>
					<view class="titile2-text3">{{ list.helpTask.openContent }}</view>
				</view>
				<view class="titile2-group2" style="background-color: #FFFFFF;"><button class="confirm-btn" @click="copyHref()">一键复制口令</button></view>
			</view>

			<view class="type3" v-if="list.helpTask.openType == 3">
				<view class="titile2-group">
					<view class="titile-text" style="width: 100px;">扫码链接</view>
					<view class="titile2-text3">需要扫描下方的二维码图片</view>
				</view>
			</view>
			<view style="margin: 16upx 0 16upx 0;background-color: #FFFFFF;" v-for="(item, index) in list.helpTaskDetailsList"
			 :key="index">
				<view class="upload" style="display: flex;margin-top: 20upx; flex-direction: row;">
					<view class="upload-text">{{ item.content }}</view>
				</view>

				<view class="uploadImage" style="display: flex; flex-direction: row;">
					<view style="width: 50%;">
						<image style="width: 100%;height: 300upx; margin-left:20upx;" @click="previewImg(item.picture)" :src="item.picture" />
					</view>
					<view style="width: 50%;" v-if="list.HelpSendOrderDetailsList[index].picture">
						<image style="width: 100%;height: 300upx; margin-left:20upx;" @click="previewImg(list.HelpSendOrderDetailsList[index].picture)"
						 :src="list.HelpSendOrderDetailsList[index].picture" />
					</view>
				</view>
			</view>
			<view  style="margin: 20upx; padding: 20upx; background: #FFFFFF;">
				<view style="font-size: 16px;color: #000000;font-weight: bold;">审核拒绝:</view>
				<view style="margin-top: 20upx;margin-left: 40upx;font-size: 15px; color: #000000;">{{ auditContent }}</view>
			</view>
		</view>
		<view v-if="this.flag == 5" style="display: flex; flex-direction: row; margin-top: 20upx;"><button style="width: 98%;"
			 type="warn" @click="save">提交维权</button></view>
	</view>
</template>

<script>
	export default {
		components: {},
		data() {
			return {
				logo: [{
						picture: ''
					},
					{
						picture: ''
					},
					{
						picture: ''
					},
					{
						picture: ''
					}
				],
				id: '',
				relation_id: '',
				array: ['10分钟', '30分钟', '1小时', '6小时', '24小时', '2天', '3天', '5天'],
				array1: ['10', '30', '60', '360', '1440', '2880', '4320', '7200'],
				time: ['12小时', '24小时', '48小时'],
				auditContent:'',
				rate: [],
				typeindex: 1,
				userState: '',
				helpTaskId: '',
				state: 1,
				helpSendOrderId: '',
				flag: 0,
				time1: ['720', '1440', '2880'],
				list: {
					helpTask: {
						label: ''
					}
				}
			};
		},
		onLoad(params) {
			this.id = params.id;
			this.relation_id = this.$queue.getData('relation_id');
			let userId = this.$queue.getData('userId');
			if (userId) {
				this.getUserInfo(userId);
				this.getListById(this.id);
			}
		},
		methods: {
			/**
			 * @param {Object} url
			 * @param {Object} titles 首页item跳转
			 */
			toNavList: function(url) {
				if (url.indexOf('/pages/') !== -1) {
					uni.navigateTo({
						url
					});
				} else {
					//#ifndef H5
					uni.navigateTo({
						url: '/pages/member/webview?url=' + url
					});
					//#endif
					//#ifdef H5
					window.location.href = url;
					//#endif
				}
			},
			rule() {
				uni.navigateTo({
					url: './rule'
				});
			},
			save() {
				let userId = this.$queue.getData('userId');
				let data = {
					sendOrderUserId: userId, //接单id
					helpTaskId: this.helpTaskId //助力活动id
				};
				//通过任务
				this.$Request.postT('/helpTask/saveHelpMaintain', data).then(res => {
					if (res.code === 0) {
						this.getListById(this.id);
						uni.showToast({
							icon: 'none',
							title: '提交成功'
						});
					} else {
						uni.showToast({
							icon: 'none',
							title: '提交失败'
						});
					}
				});
			},
			/**
			 * 复制链接
			 */
			copyHref() {
				uni.setClipboardData({
					data: this.list.helpTask.openContent,
					success: r => {
						this.$queue.showToast('复制成功');
					}
				});
			},
			getUserInfo(userId) {
				this.$Request.postJson('/app/selectUserById?userId=' + userId).then(res => {
					if (res.code === 0) {
						this.userState = res.data.member;
					} else {
						uni.showModal({
							showCancel: false,
							title: '登录失败',
							content: res.msg
						});
						this.$queue.logout();
					}
					uni.hideLoading();
				});
			},
			getListById(id) {
				uni.showLoading({
					title: '加载中...'
				});
				let userId = this.$queue.getData('userId');
				let data = {
					userId: userId,
					id: id
				};
				var userStateByMoney = 1;
				this.$Request.getT('/helpTask/selectHelpTaskDetails', data).then(res => {
					if (res.code === 0) {
						this.logo = [];
						for (var i = 0; i < res.data.helpTaskDetailsList.length; i++) {
							const icon = {};
							icon.picture = '';
							icon.sort = '';
							icon.isVerify = res.data.helpTaskDetailsList[i].isVerify;
							this.logo.push(icon);
						}
						if (res.data.HelpSendOrderDetailsList) {
							for (var i = 0; i < res.data.HelpSendOrderDetailsList.length; i++) {
								this.logo[i].picture = res.data.HelpSendOrderDetailsList[i].picture
							}
						}

						for (var i = 0; i < res.data.rate.length; i++) {
							if (res.data.rate[i].id === this.userState) {
								userStateByMoney = res.data.rate[i].rate + res.data.rate[4].rate;
							}
							this.rate.push(res.data.rate[i]);
						}
						res.data.helpTask.taskOriginalPrice = res.data.helpTask.taskOriginalPrice - res.data.helpTask.taskOriginalPrice *
							userStateByMoney;
						res.data.helpTask.taskOriginalPrice = parseFloat(res.data.helpTask.taskOriginalPrice).toFixed(2);
						this.list = res.data;
						this.helpTaskId = res.data.helpTask.id;
						this.flag = res.data.flag;
						this.state = res.data.helpTask.state;
						this.helpSendOrderId = this.flag != 1 ? res.data.helpSendOrder.id : '';
						this.auditContent = res.data.helpSendOrder.auditContent;
						for (var i = 0; i < this.time1.length; i++) {
							if (this.list.helpTask.auditTime == this.time1[i]) {
								this.list.helpTask.auditTime = this.time[i];
								break;
							}
						}

						for (var i = 0; i < this.array1.length; i++) {
							if (this.list.helpTask.restrictTime == this.array1[i]) {
								this.list.helpTask.restrictTime = this.array[i];
								break;
							}
						}
					}
					uni.hideLoading();
				});
			},
			previewImg(logourl) {
				let _this = this;
				let imgsArray = [];
				imgsArray[0] = logourl;
				uni.previewImage({
					current: 0,
					urls: imgsArray
				});
			},
			uploadImg(index) {
				let that = this;
				uni.chooseImage({
					count: 1, //默认9
					sizeType: ['original', 'compressed'], //可以指定是原图还是压缩图，默认二者都有
					sourceType: ['album', 'camera'], //从相册选择
					success: function(res) {
						console.log('iamgeFile____:' + index);
						that.logo[index].picture = res.tempFilePaths[0];
						that.logo[index].sort = index;
						let token = uni.getStorageSync('token');
						uni.uploadFile({
							url: 'http://admin.jihuisheng.cn/tao/file/upload', //仅为示例，非真实的接口地址
							// url: 'http://192.168.1.110:8081/file/upload', //仅为示例，非真实的接口地址
							filePath: res.tempFilePaths[0],
							header: {
								'content-type': 'multipart/form-data',
								token: token
							},
							name: 'file',
							success: uploadFileRes => {
								that.list.helpTaskDetailsList[index].picture = uploadFileRes.data;
							}
						});
					}
				});
			},
			goBack() {
				uni.navigateBack();
			},
			toLogout() {
				var value = this.titile2 - text3.detail.value;
				console.log(value);
				uni.setClipboardData({
					data: value
				});
			}
		}
	};
</script>

<style lang="less" scoped>
	page {
		background: #FFFFFF;
	}

	.upload {
		background-color: #ffffff;
		padding: 10upx;

		.upload-image {
			height: 40upx;
			width: 50upx;
			margin-top: 10upx;
			margin-left: 10upx;
		}

		.upload-text {
			font-size: 17px;
			color: #000000;
			margin-left: 10upx;
		}
	}

	.tui-btn-box {
		padding: 40rpx 50rpx;
		box-sizing: border-box;
	}

	.header {
		height: 88upx;
		background-color: #fff;
		display: flex;
		align-items: center;
		padding-top: 13%;

		.header-title {
			width: calc(100% - 72upx);
			text-align: center;
			color: #0f1930;
			font-size: 32upx;
			font-weight: bold;
		}

		.detail {
			font-size: 32rpx;
			width: 104rpx;
		}
	}

	.newTask {
		background: #FFFFFF;

		.titile1 {
			background-color: #ffffff;
			padding: 3%;
			display: flex;
			flex-direction: row;
			padding-top: 60upx;
			width: 100%;
		}

		.titile1-image {
			height: 100upx;
			width: 100upx;
		}

		.titile1-group1 {
			margin-left: 20upx;
			width: 200upx;

			.titile-group1-text1 {
				font-size: 15px;
				font-weight: bold;
				margin-top: 10upx;
			}

			.titile-group1-text2 {
				font-size: 12px;
				color: #7a7a7a;
				justify-content: right;
				margin-top: 20upx;
			}
		}

		.titile2-group {
			display: flex;
			flex-direction: range(start, end, step);
			font-size: 14px;
			padding: 2%;
			margin-top: 1upx;
			margin-top: 1upx;
			background-color: #ffffff;
		}

		.titile2-text1 {
			margin-left: 40upx;
			color: #7a7a7a;
		}

		.titile2-text3 {
			margin-left: 95upx;

			flex-wrap: wrap;
		}

		.titile2-group2 {
			padding-top: 20upx;
			padding-bottom: 20upx;
		}

		.confirm-btn {
			width: 150px;
			height: 30px;
			line-height: 32px;
			border-radius: 30px;
			background-color: #34c9f5;
			color: #fff;
			font-size: 14px;
		}
	}
</style>
