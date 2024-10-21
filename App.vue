<script>
import Vue from 'vue';
export default {
	methods: {
		async getClipboardData() {
			let that = this;
			uni.getClipboardData({
				success: function(res) {
					if (res.data && res.data.length == 10) {
						that.$queue.setData('relation', res.data);
					}
					if (
						res.data &&
						res.data.length != 6 &&
						res.data.length != 10 &&
						res.data.indexOf('给你说个京东、淘宝') == -1 &&
						res.data.indexOf('椱ァ製这段文字') == -1 &&
						res.data.indexOf('/pages/member/publisher') == -1 &&
						res.data.indexOf('椱ァ製此信息') == -1
						
					) {
						that.$Request.getT('/jd/doTpwdCoverts?tpwd=' + res.data).then(ress => {
							if (ress) {
								uni.setClipboardData({
									data: '',
									success: r => {
										uni.showToast({
											icon: 'loading',
											title: '正在搜索...',
											duration: 1000
										});
									}
								});
								uni.navigateTo({
									url: '/pages/detail/goodsinfo?itemid=' + ress
								});
							} else {
								let id = '';
								if (res.data.indexOf('&id=') != -1) {
									id = res.data.split('&id=')[1].split('&')[0];
								}
								if (res.data.indexOf('?id=') != -1) {
									id = res.data.split('?id=')[1].split('&')[0];
								}
								if (id) {
									uni.navigateTo({
										url: '/pages/detail/goodsinfo?itemid=' + id
									});
									uni.setClipboardData({
										data: '',
										success: r => {
											uni.showToast({
												icon: 'loading',
												title: '正在搜索...',
												duration: 1000
											});
										}
									});
								} else {
									uni.showModal({
										showCancel: true,
										cancelText: '取消',
										confirmText: '搜索',
										title: '是否搜索以下商品？',
										content: res.data,
										success: ress => {
											if (ress.confirm) {
												uni.navigateTo({
													url: '/pages/search/search?keywords=' + res.data
												});
												uni.setClipboardData({
													data: '',
													success: r => {
														uni.showToast({
															icon: 'loading',
															title: '正在搜索...',
															duration: 1000
														});
													}
												});
											} else {
												uni.setClipboardData({
													data: '',
													success: r => {
														uni.showToast({
															icon: 'none',
															title: '已取消',
															duration: 500
														});
													}
												});
											}
										}
									});
								}
							}
						});
					}
				},
				fail: function(res) {}
			});
		}
	},
	onLaunch: function() {
		let that = this;
		//获取全局邀请码
		that.$Request.getT('/common/type/4').then(res => {
			if (res.code == 0) {
				if (res.data && res.data.value) {
					this.$queue.setData('publicRelation', res.data.value);
				}
			}
		});
		//获取淘宝APPID
		that.$Request.getT('/common/type/6').then(res => {
			if (res.code == 0) {
				if (res.data && res.data.value) {
					this.$queue.setData('taobaoAppid', res.data.value);
				}
			}
		});
		//获取淘宝秘钥
		that.$Request.getT('/common/type/7').then(res => {
			if (res.code == 0) {
				if (res.data && res.data.value) {
					this.$queue.setData('taobaoSecret', res.data.value);
				}
			}
		});

		//获取淘宝pid
		that.$Request.getT('/common/type/9').then(res => {
			if (res.code == 0) {
				if (res.data && res.data.value) {
					this.$queue.setData('taobaoPid', res.data.value);
				}
			}
		});
		//获取好单库key
		that.$Request.getT('/common/type/10').then(res => {
			if (res.code == 0) {
				if (res.data && res.data.value) {
					this.$queue.setData('haodankuKey', res.data.value);
				}
			}
		});
		//获取淘宝名字
		that.$Request.getT('/common/type/11').then(res => {
			if (res.code == 0) {
				if (res.data && res.data.value) {
					this.$queue.setData('taobaoName', res.data.value);
				}
			}
		});

		//获取APP下载地址
		that.$Request.getT('/common/type/25').then(res => {
			if (res.code == 0) {
				if (res.data && res.data.value) {
					this.$queue.setData('appurl', res.data.value);
				}
			}
		});
        //关键字过滤
		that.$Request.getT('/common/type/67').then(res => {
			if (res.code == 0) {
				if (res.data && res.data.value) {
					this.$queue.setData('searchKeys', res.data.value);
				}
			}
		});
		//#ifdef H5
		let width = window.screen.width;
		if (window.location.href.indexOf('?invitation=') !== -1 || window.location.href.indexOf('&invitation=') !== -1) {
			if (window.location.href.indexOf('?invitation=') !== -1) {
				this.$queue.setData('relation', window.location.href.split('?invitation=')[1].split('&')[0]);
			} else {
				this.$queue.setData('relation', window.location.href.split('&invitation=')[1].split('&')[0]);
			}
		}
		//#endif
		//#ifdef APP-PLUS
		// APP检测更新 具体可以参考：https://ask.dcloud.net.cn/article/35667
		plus.screen.lockOrientation('portrait-primary'); //竖屏正方向锁定

		const updated = uni.getStorageSync('updated'); // 尝试读取storage

		if (updated.completed === true) {
			// 如果上次刚更新过
			// 删除安装包及安装记录
			console.log('安装记录被删除，更新成功');
			uni.removeSavedFile({
				filePath: updated.packgePath,
				success: res => {
					uni.removeStorageSync('updated');
				}
			});
		} else if (updated.completed === false) {
			uni.removeStorageSync('updated');
			plus.runtime.install(updated.packgePath, {
				force: true
			});
			uni.setStorage({
				key: 'updated',
				data: {
					completed: true,
					packgePath: updated.packgePath
				},
				success: res => {
					console.log('成功安装上次的更新，应用需要重启才能继续完成');
				}
			});
			uni.showModal({
				title: '提示',
				content: '应用将重启以完成更新',
				showCancel: false,
				complete: () => {
					plus.runtime.restart();
				}
			});
		} else {
			plus.runtime.getProperty(plus.runtime.appid, widgetInfo => {
				this.$Request.getT('/appinfo/').then(res => {
					res = res.data[0];
					if (res.wgtUrl && widgetInfo.version < res.version) {
						let downloadLink = '';
						let androidLink = res.androidWgtUrl;
						let iosLink = res.iosWgtUrl;
						let ready = false;
						if (res.wgtUrl.match(RegExp(/.wgt/))) {
							// 判断系统类型
							if (plus.os.name.toLowerCase() === 'android') {
								console.log('安卓系统');
								if (androidLink && androidLink !== '#') {
									// 我这里默认#也是没有地址，请根据业务自行修改
									console.log('发现下载地址');
									// 安卓：创建下载任务
									if (androidLink.match(RegExp(/.wgt/))) {
										console.log('确认wgt热更新包');
										downloadLink = androidLink;
										ready = true;
									} else {
										console.log('安卓推荐.wgt强制更新，.apk的强制更新请您自行修改程序');
									}
								} else {
									console.log('下载地址是空的，无法继续');
								}
							} else {
								console.log('苹果系统');
								if (iosLink && iosLink !== '#') {
									// 我这里默认#也是没有地址，请根据业务自行修改
									console.log('发现下载地址');
									// 苹果(A)：进行热更新（如果iosLink是wgt更新包的下载地址）判断文件名中是否含有.wgt
									if (iosLink.match(RegExp(/.wgt/))) {
										console.log('确认wgt热更新包');
										downloadLink = iosLink;
										ready = true;
									} else {
										console.log('苹果只支持.wgt强制更新');
									}
								} else {
									console.log('下载地址是空的，无法继续');
								}
							}
							if (ready) {
								console.log('任务开始');
								let downloadTask = uni.downloadFile({
									url: downloadLink,
									success: res => {
										if (res.statusCode === 200) {
											// 保存下载的安装包
											console.log('保存安装包');
											uni.saveFile({
												tempFilePath: res.tempFilePath,
												success: res => {
													const packgePath = res.savedFilePath;
													// 保存更新记录到stroage，下次启动app时安装更新
													uni.setStorage({
														key: 'updated',
														data: {
															completed: false,
															packgePath: packgePath
														},
														success: () => {
															console.log('成功保存记录');
														}
													});
													// 任务完成，关闭下载任务
													console.log('任务完成，关闭下载任务，下一次启动应用时将安装更新');
													downloadTask.abort();
													downloadTask = null;
												}
											});
										}
									}
								});
							} else {
								console.log('下载地址未准备，无法开启下载任务');
							}
						} else {
							if (res.method == 'true') {
								uni.showModal({
									showCancel: false,
									confirmText: '立即更新',
									title: '发现新版本',
									content: res.des,
									success: res => {
										if (res.confirm) {
											this.$queue.showLoading('下载中...');
											if (uni.getSystemInfoSync().platform == 'android') {
												uni.downloadFile({
													url: androidLink,
													success: downloadResult => {
														if (downloadResult.statusCode === 200) {
															plus.runtime.install(
																downloadResult.tempFilePath,
																{
																	force: false
																},
																d => {
																	console.log('install success...');
																	plus.runtime.restart();
																},
																e => {
																	console.error('install fail...');
																}
															);
														}
													}
												});
											}
											if (uni.getSystemInfoSync().platform == 'ios') {
												plus.runtime.openURL(iosLink, function(res) {});
											}
										} else if (res.cancel) {
											console.log('取消');
										}
									}
								});
							} else {
								uni.showModal({
									title: '发现新版本',
									confirmText: '立即更新',
									cancelText: '下次更新',
									content: res.des,
									success: res => {
										if (res.confirm) {
											this.$queue.showLoading('下载中...');
											if (uni.getSystemInfoSync().platform == 'android') {
												uni.downloadFile({
													url: androidLink,
													success: downloadResult => {
														if (downloadResult.statusCode === 200) {
															plus.runtime.install(
																downloadResult.tempFilePath,
																{
																	force: false
																},
																d => {
																	console.log('install success...');
																	plus.runtime.restart();
																},
																e => {
																	console.error('install fail...');
																}
															);
														}
													}
												});
											}
											if (uni.getSystemInfoSync().platform == 'ios') {
												plus.runtime.openURL(iosLink, function(res) {});
											}
										} else if (res.cancel) {
											console.log('取消');
										}
									}
								});
							}
						}
					}
				});
			});
		}

		//#endif
		//#ifdef H5

		let relation = this.$queue.getInvitation();
		if (window.location.href.indexOf('?invitation=') !== -1 || window.location.href.indexOf('&invitation=') !== -1) {
			if (window.location.href.indexOf('?invitation=') !== -1) {
				relation = window.location.href.split('?invitation=')[1].split('&')[0];
				this.$queue.setData('relation', window.location.href.split('?invitation=')[1].split('&')[0]);
			} else {
				relation = window.location.href.split('&invitation=')[1].split('&')[0];
				this.$queue.setData('relation', window.location.href.split('&invitation=')[1].split('&')[0]);
			}
		}
		that.$Request.getT('/common/type/47').then(res => {
			if (res.status == 0) {
				if (res.data && res.data.value) {
					if (res.data.value === '是') {
						let ua = navigator.userAgent.toLowerCase();
						if (ua.indexOf('micromessenger') === -1) {
							uni.reLaunch({
								url: '/pages/member/erweimaRegister'
							});
						} else {
							if (this.$queue.getData('openid')) {
								this.$Request.get('/tao/wx/follow/' + this.$queue.getData('openid')).then(res => {
									if (res === false) {
										uni.reLaunch({
											url: '/pages/member/erweimaRegister'
										});
									}
								});
							}
						}
					}
				}
			}
		});
		//#endif
	},

	onShow: function() {
		// #ifdef APP-PLUS
		this.getClipboardData();
		if (uni.getSystemInfoSync().platform == 'android') {
			let clientid = plus.push.getClientInfo().clientid;
			console.error(clientid);
			let userId = this.$queue.getData('userId');
			if (userId) {
				this.$Request.postT('/user/updateClientId?clientId=' + clientid + '&userId=' + userId).then(res => {});
			}
		}

		//#endif
	},
	onHide: function() {}
};
</script>

<style lang="scss">
/*每个页面公共css */
@import 'colorui/main.css';
@import 'colorui/icon.css';
@import './static/css/common.css';
@import './static/css/simplepro.css';

/*
            全局公共样式和字体图标
        */
@font-face {
	font-family: yticon;
	font-weight: normal;
	font-style: normal;
	src: url('https://at.alicdn.com/t/font_1078604_w4kpxh0rafi.ttf') format('truetype');
}

.yticon {
	font-family: 'yticon' !important;
	font-size: 16px;
	font-style: normal;
	-webkit-font-smoothing: antialiased;
	-moz-osx-font-smoothing: grayscale;
}

.nav-li text {
	position: absolute;
	right: 30 upx;
	top: 30 upx;
	font-size: 52 upx;
	width: 60 upx;
	height: 60 upx;
	text-align: center;
	line-height: 60 upx;
}

uni-tabbar .uni-tabbar__icon {
	width: 20px;
	height: 20px;
}

@keyframes show {
	0% {
		transform: translateY(-50px);
	}

	60% {
		transform: translateY(40 upx);
	}

	100% {
		transform: translateY(0px);
	}
}

@-webkit-keyframes show {
	0% {
		transform: translateY(-50px);
	}

	60% {
		transform: translateY(40 upx);
	}

	100% {
		transform: translateY(0px);
	}
}

.icon-yiguoqi1:before {
	content: '\e700';
}

.icon-iconfontshanchu1:before {
	content: '\e619';
}

.icon-iconfontweixin:before {
	content: '\e611';
}

.icon-alipay:before {
	content: '\e636';
}

.icon-shang:before {
	content: '\e624';
}

.icon-shanchu4:before {
	content: '\e622';
}

.icon-xiaoxi:before {
	content: '\e618';
}

.icon-jiantour-copy:before {
	content: '\e600';
}

.icon-fenxiang2:before {
	content: '\e61e';
}

.icon-pingjia:before {
	content: '\e67b';
}

.icon-daifukuan:before {
	content: '\e68f';
}

.icon-pinglun-copy:before {
	content: '\e612';
}

.icon-dianhua-copy:before {
	content: '\e621';
}

/*.icon-shoucang:before {*/
/*    content: "\e645";*/
/*}*/

.icon-xuanzhong2:before {
	content: '\e62f';
}

.icon-gouwuche_:before {
	content: '\e630';
}

.icon-icon-test:before {
	content: '\e60c';
}

.icon-icon-test1:before {
	content: '\e632';
}

.icon-bianji:before {
	content: '\e646';
}

.icon-jiazailoading-A:before {
	content: '\e8fc';
}

.icon-zuoshang:before {
	content: '\e613';
}

.icon-jia2:before {
	content: '\e60a';
}

.icon-huifu:before {
	content: '\e68b';
}

.icon-arrow-fine-up:before {
	content: '\e601';
}

.icon-hot:before {
	content: '\e60e';
}

.icon-lishijilu:before {
	content: '\e6b9';
}

.icon-zhengxinchaxun-zhifubaoceping-:before {
	content: '\e616';
}

.icon-naozhong:before {
	content: '\e64a';
}

.icon-xiatubiao--copy:before {
	content: '\e608';
}

.icon-shoucang_xuanzhongzhuangtai:before {
	content: '\e6a9';
}

.icon-jia1:before {
	content: '\e61c';
}

.icon-bangzhu1:before {
	content: '\e63d';
}

.icon-arrow-left-bottom:before {
	content: '\e602';
}

.icon-arrow-right-bottom:before {
	content: '\e603';
}

.icon-arrow-left-top:before {
	content: '\e604';
}

.icon-icon--:before {
	content: '\e744';
}

.icon-zuojiantou-up:before {
	content: '\e605';
}

.icon-xia:before {
	content: '\e62d';
}

.icon--jianhao:before {
	content: '\e60b';
}

.icon-weixinzhifu:before {
	content: '\e61a';
}

.icon-comment:before {
	content: '\e64f';
}

.icon-weixin:before {
	content: '\e61f';
}

.icon-fenlei1:before {
	content: '\e620';
}

.icon-erjiye-yucunkuan:before {
	content: '\e623';
}

.icon-Group-:before {
	content: '\e688';
}

.icon-you:before {
	content: '\e606';
}

.icon-forward:before {
	content: '\e607';
}

.icon-tuijian:before {
	content: '\e610';
}

.icon-bangzhu:before {
	content: '\e679';
}

.icon-share:before {
	content: '\e656';
}

.icon-yiguoqi:before {
	content: '\e997';
}

.icon-shezhi1:before {
	content: '\e61d';
}

.icon-fork:before {
	content: '\e61b';
}

.icon-kafei:before {
	content: '\e66a';
}

.icon-iLinkapp-:before {
	content: '\e654';
}

.icon-saomiao:before {
	content: '\e60d';
}

.icon-shezhi:before {
	content: '\e60f';
}

.icon-shouhoutuikuan:before {
	content: '\e631';
}

.icon-gouwuche:before {
	content: '\e609';
}

.icon-dizhi:before {
	content: '\e614';
}

.icon-fenlei:before {
	content: '\e706';
}

.icon-xingxing:before {
	content: '\e70b';
}

.icon-tuandui:before {
	content: '\e633';
}

.icon-zuanshi:before {
	content: '\e615';
}

.icon-zuo:before {
	content: '\e63c';
}

.icon-shoucang2:before {
	content: '\e62e';
}

.icon-shouhuodizhi:before {
	content: '\e712';
}

.icon-yishouhuo:before {
	content: '\e71a';
}

.icon-dianzan-ash:before {
	content: '\e617';
}
</style>
