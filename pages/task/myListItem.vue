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
				<view v-if="state === 0" style="width: 150upx;color: #999999;font-size:26upx;margin-top:5upx;">待审核</view>
				<view v-if="state === 1" style="color: green;font-size:26upx;margin-top:5upx;">已通过</view>
				<view v-if="state === 2" style="color: red;font-size:26upx;margin-top:5upx;">已拒绝</view>
				<view v-if="state === 3" style="color: green;font-size:26upx;margin-top:5upx;">已结算</view>
			</view>
		</view>
		<!-- 第二部分 -->
		<view class="titile2" style="box-shadow: rgba(183, 183, 183, 0.3) 1px 1px 10px 1px;border-radius: 20upx;margin: 0upx 20upx 20upx 20upx; padding: 0upx 20upx 20upx 20upx; background: #FFFFFF;">
			<view style="color: #000000; font-weight: bold; font-size: 36upx; padding: 20upx 0 10upx 10upx;">任务详情</view>
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
				<view class="upload" style="display: flex;margin-top: 20upx; flex-direction: row; justify-content: center;">
					<view class="upload-text">{{ item.content }}</view>
				</view>

				<view class="uploadImage" style="display: flex; flex-direction: row;">
					<view style="width: 60%;">
						<image style="width: 100%;height: 300upx; margin-left:120upx;" @click="previewImg(item.picture)" :src="item.picture" />
					</view>
				</view>
			</view>
			<view v-if="state === 2">
				<view style="display: flex;flex-direction: row;">
					<view class="upload-text" style="margin: 10upx; ">拒绝理由：</view>
				</view>
				<view style="font-size: 28upx;color: #000000;padding: 20upx 20upx 20upx 40upx;">{{auditText}}</view>
			</view>
		</view>
	</view>
</template>

<script>
	import configdata from '../../common/config.js';
	export default {
		components: {},
		data() {
			return {
				id: '',
				name: '',
				relation_id: '',
				array: ['10分钟', '30分钟', '1小时', '6小时', '24小时', '2天', '3天', '5天'],
				array1: ['10', '30', '60', '360', '1440', '2880', '4320', '7200'],
				time: ['12小时', '24小时', '48小时'],
				rate: [],
				auditText: '',
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
			let userId = this.$queue.getData('userId');
			if (userId) {
				this.getListById(this.id);
			}
		},
		onNavigationBarButtonTap() {
			this.rule();
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
			getListById(id) {
				uni.showLoading({
					title: '加载中...'
				});
				let userId = this.$queue.getData('userId');
				let data = {
					userId: userId,
					id: id
				};
				this.$Request.getT('/helpTask/selectHelpTaskDetails', data).then(res => {
					if (res.code === 0) {
						this.list = res.data;
						this.helpTaskId = res.data.helpTask.id;
						this.flag = res.data.flag;
						this.state = res.data.helpTakeOrder.state;
						this.auditText = res.data.helpTakeOrder.auditContent;
						this.helpSendOrderId = this.flag != 1 ? res.data.helpSendOrder.id : '';
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
			config: function(name) {
				var info = null;
				if (name) {
					var name2 = name.split("."); //字符分割
					if (name2.length > 1) {
						info = configdata[name2[0]][name2[1]] || null;
					} else {
						info = configdata[name] || null;
					}
					if (info == null) {
						let web_config = cache.get("web_config");
						if (web_config) {
							if (name2.length > 1) {
								info = web_config[name2[0]][name2[1]] || null;
							} else {
								info = web_config[name] || null;
							}
						}
					}
				}
				return info;
			},
			rule() {
				uni.navigateTo({
					url: './rule'
				});
			},
			goBack() {
				uni.navigateBack();
			},
			toLogout() {
				var value = this.titile2 - text3.detail.value;
				uni.setClipboardData({
					data: value
				});
			}
		}
	};
</script>

<style lang="less" scoped>
	page {
		background-color: #FFFFFF;
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
			font-size: 34upx;
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
				font-size: 30upx;
				font-weight: bold;
				margin-top: 10upx;
			}

			.titile-group1-text2 {
				font-size: 24upx;
				color: #7a7a7a;
				justify-content: right;
				margin-top: 20upx;
			}
		}

		.titile2-group {
			display: flex;
			flex-direction: range(start, end, step);
			font-size: 28upx;
			padding: 2%;
			margin-top: 1upx;
			margin-top: 1upx;
			background-color: #ffffff;
		}

		.titile2-text {
			color: #7a7a7a;
			font-size: 28upx;
		}

		.titile2-text1 {
			margin-left: 40upx;
			color: #000000;
			font-size: 28upx;
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
			width: 300upx;
			height: 60upx;
			line-height: 60upx;
			border-radius: 30px;
			background-color: #34c9f5;
			color: #fff;
			font-size: 28upx;
		}
	}
</style>
