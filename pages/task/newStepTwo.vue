<template>
	<view class="newStepTwo">
		<tui-modal :show="modal"  @cancel="hide" 
		content="请设置您的手机号码,必要时客服将和您联系" 
		:button="button"
		@click="handleClick"
		>
		</tui-modal>
		
		<form @submit="formSubmit" @reset="formReset" v-show="!modal">
				<tui-list-cell :hover="false">
					<view class="tui-line-cell">
						<view class="tui-title">会员昵称</view>
						<input placeholder-class="tui-phcolor" class="tui-input" name="nickname" placeholder="请输入昵称" maxlength="50" type="text" value="哈哈哈" />
					</view>
				</tui-list-cell>
				<tui-list-cell :hover="false">
					<view class="tui-line-cell">
						<view class="tui-title">手机号</view>
						<input placeholder-class="tui-phcolor" class="tui-input" name="mobile" placeholder="请输入您的QQ号码" maxlength="11" type="number" />
					</view>
				</tui-list-cell>
				<tui-list-cell :hover="false">
					<view class="tui-line-cell">
						<view class="tui-title">支付宝</view>
						<input placeholder-class="tui-phcolor" class="tui-input" name="alipay" placeholder="请输入您的支付宝" maxlength="50" type="number" />
					</view>
				</tui-list-cell>
				<tui-list-cell :hover="false">
					<view class="tui-line-cell">
						<view class="tui-title">真实名字</view>
						<input placeholder-class="tui-phcolor" class="tui-input" name="name" placeholder="请输入您的名字" maxlength="50" type="text" />
					</view>
				</tui-list-cell>
			
	
				<view class="tui-btn-box">
					<button class="tui-button-primary" hover-class="tui-button-hover" formType="submit" type="primary" style="color: #FFFFFF;background: #E6325C;">保存</button>
				</view>
			</form>
		
	</view>
</template>

<script>
	const form = require("@/components/tui-validation/tui-validation.js")
	
	import tuiModal from "@/components/thorui/tui-modal/tui-modal.vue"
	import tuiListCell from "@/components/thorui/tui-list-cell/tui-list-cell.vue"
	

	export default {
		components: {
			tuiModal,
			tuiListCell,
		},
		data() {
			return {
				modal:true,
				button: [
					{
						text: '确定',
						type: 'red'
					}
				],
			};
		},
		methods:{
			handleClick(e) {
				let index = e.index;
				this.hide();
			},
			hide() {
				this.modal = false;
			},
			formSubmit: function(e) {
				var that = this;
				//表单规则
				let rules = [{
					name: "nickname",
					rule: ["required" ], //可使用区间，此处主要测试功能
					msg: ["请输入姓名"]
				}, {
					name: "qq",
					rule: [ "isNum", ],
					msg: ["qq号码是数字"]
				}, {
					name: "wechat",
					rule: [],
					msg: []
				}, {
					name: "mobile",
					rule: ["isMobile"],
					msg: ["请输入正确的手机号"]
				}, {
					name: "alipay",
					rule: [],
					msg: []
				}, {
					name: "name",
					rule: ["isChinese", "minLength:2", "maxLength:6"], //可使用区间，此处主要测试功能
					msg: ["请输入姓名", "姓名必须全部为中文", "姓名必须2个或以上字符", "姓名不能超过6个字符"]
				}];
				//进行表单检查
				let formData = e.detail.value;
				if(formData){
					let checkRes = form.validation(formData, rules);
					if (!checkRes) {
						uni.showToast({
							title: "友情提示，修改成功",
							icon: "none"
						});
							setTimeout(function(){
								uni.switchTab({
									url: '/pages/task/index'
								});
							},2000);
						
					} else {
						uni.showToast({
							title: checkRes,
							icon: "none"
						});
					}
				}
				
			},
			
		},
	}
</script>

<style lang="less">
.newStepTwo{
	background: #F2F2F2;
	.tui-line-cell {
		width: 100%;
		box-sizing: border-box;
		display: flex;
		align-items: center;
		justify-content: space-between;
	}
	
	.tui-title {
		line-height: 32rpx;
		min-width: 120rpx;
		flex-shrink: 0;
	}
	
	.tui-input {
		font-size: 32rpx;
		color: #333;
		padding-left: 20rpx;
		flex: 1;
	}
	
	.radio-group {
		margin-left: auto;
		transform: scale(0.8);
		transform-origin: 100% center;
		flex-shrink: 0;
	}
	
	.tui-radio {
		display: inline-block;
		padding-left: 28rpx;
		font-size: 36rpx;
		vertical-align: middle;
	}
	
	
	.tui-btn-box {
		padding: 40rpx 50rpx;
		box-sizing: border-box;
	}
	
	.tui-button-gray {
		margin-top: 30rpx;
	}
	
	.tui-tips {
		padding: 30rpx;
		color: #999;
		font-size: 24rpx;
	}
}
</style>
