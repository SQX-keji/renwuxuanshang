<template>
    <view style="font-size: 14px;line-height: 26px;padding: 32upx;background: #FFFFFF;" class="home1">
        <view style="font-size: 16px;font-weight: bold">派单规则</view>
        <view style="text-align: left;text-indent: 2rem;"> 1.发布任务前需要保证账户余额充足，审核通过后才会扣任务保证金；
        </view>
        <view style="text-align: left;text-indent: 2rem;">2. 用户提交任务后，您需要在自己设置的时间内审核，超时默认通过；
        </view>
        <view style="text-align: left;text-indent: 2rem;">
            3. 如用户提交的任务不符合要求，可拒绝等待用户补充资料，恶意拒绝，严重影响用户的权益，会被扣除当前任务的所有保证金，且平台保留封号处理的权限；
        </view>
        <view style="text-align: left;text-indent: 2rem;">
            4. 言语规范：严禁粗话，脏话及歧视诅咒言语，如有发现直接按接单方符合要求处理；
        </view>
        <view style="font-size: 16px;font-weight: bold">2、服务内容</view>
        <view style="text-align: left;text-indent: 2rem;">
            5. 不得发布与本平台提供服务高度相似的任务；
        </view>
        <view style="text-align: left;text-indent: 2rem;">
            6. 禁止向外拉人行为，让接单者加微信QQ或进群等拉人行为，一经发现永久封号，平台会通过技术以及巡查的方式查证；
        </view>
        <view style="text-align: left;text-indent: 2rem;">
            7. 严禁任务造假，发布不可能完成的任务，或通过率极低的，完成任务周期需要较长的，任务中又没有明确提示的，都将按虚假任务处理；
        </view>
        <view style="text-align: left;text-indent: 2rem;"> 8. 省钱兄平台会尽最大的努力秉承公正裁决，但不能保证每次裁决均为客观正确，因无法查阅贵平台数据，只能根据截图本身做出裁定，敬请知晓；
        </view>
        <view style="text-align: left;text-indent: 2rem;">
            9. 任务赏金设置合理，任务最低赏金会根据行情价格进行调整，省钱兄平台均为自然人，不是机器和刷手，几毛钱的任务又让注册又让绑定，请不要抱怨无人接单，互利互惠才是正理；
        </view>
        <view style="text-align: left;text-indent: 2rem;">10. 发布任务时，要求接单方必须完成的数据，必须写在步骤和说明中；验证图与实际不符合，用个人微信二维码代替公众号二维码或下载链接、伪造任务链接等蒙骗过关的，一经发现立即冻结任务，余额不退，严重者封号处理；
        </view>
        <view style="text-align: left;text-indent: 2rem;"> 11. 不认真审核或者以非正当理由故意拒绝会被扣除当前任务部分或全部的赏金；
        </view>
        <view style="font-size: 16px;font-weight: bold"> 系统规则</view>

        <view style="text-align: left;text-indent: 2rem;">
            1. 任务发布后内容不能更改，可修改任务数量和结束时间，数量增加后任务排序会刷新
        </view>
        <view style="text-align: left;text-indent: 2rem;">
            2. 任务结束后（没有进行中和维权），平台会按规定时间清算，或者自行申请结算；
        </view>
        <view style="text-align: left;text-indent: 2rem;">
            3. 发布任务收取一定的服务费；
        </view>
        <view style="text-align: left;text-indent: 2rem;">4. 一天能提现一次，当前平台补贴期间免手续费，后续收取1%的手续费；
        </view>
        <view style="text-align: left;text-indent: 2rem;">5. 审核时间：早上8:30~23:00，审核时间一般2小时内，部分赏金低或难审核的任务除外；
        </view>
        <view style="text-align: left;text-indent: 2rem;">6. 任务结束后，数据保留30天，超过30天系统自动清除，需要保存图片等相关数据的请提现备份；
        </view>
        <view style="text-align: left;text-indent: 2rem;">注：本规则会根据业务发展的需要适时作出调整和修改，最终解释权归省钱兄APP所有。
        </view>
        <view style="font-size: 16px;font-weight: bold"> 常见问题</view>
        <view style="text-align: left;text-indent: 2rem;">1.派单方通过任务后，是否还可以继续维权？
        </view>
        <view style="text-align: left;text-indent: 2rem;">
            答：不可以，派单方通过任务后，赏金直接到接单方账户上，不能继续维权了，审核时要注意哦。
        </view>
        <view style="text-align: left;text-indent: 2rem;"> 2.我的派单列表中存在如下状态不能结算？</view>
        <view style="text-align: left;text-indent: 2rem;">答：有进行中的、拒绝的（接单方有48小时申诉时间，超时默认放弃）、维权中的
        </view>
        <view style="text-align: left;text-indent: 2rem;">3.派单的任务什么时候可以结算？
        </view>
        <view style="text-align: left;text-indent: 2rem;">答：派单的任务上线后，以下几种情形都可以申请结算。
        </view>
        <view style="text-align: left;text-indent: 2rem;">1）没有人接
        </view>
        <view style="text-align: left;text-indent: 2rem;">2）没有进行中的任务，且没有正在维权中的任务
        </view>
        <view style="text-align: left;text-indent: 2rem;">举例说明：如果有人接了任务，但是还没有提交，这时需要等这个任务完成后才可以结算；如果存在维权中的任务，这个也是要等维权流程结束后才可以申请结算。 除了手动结算外，系统在您设置的任务截止时间后，也会自动结算该任务
        </view>
        <view style="text-align: left;text-indent: 2rem;">（5）地震、战争、严重自然灾害及不可抗力和其他不可预见的特殊情况。
        </view>
        <view style="text-align: left;text-indent: 2rem;"> 4.什么时候可以申请客服介入？</view>

        <view style="text-align: left;text-indent: 2rem;">
            答：派单方首次拒绝后，接单方可以继续补充资料，接单方补充资料后，派单方可以继续拒绝或者申请客服介入。建议先跟接单方协商处理，如果遇到不可理喻的接单方再申请客服介入。
        </view>
        <view style="text-align: left;text-indent: 2rem;"> 5.为什么我发布的任务会被平台拒绝？</view>
        <view style="text-align: left;text-indent: 2rem;"> 答：首先查看下派单规则，是否因为任务违反了有关的规定，另外，任务被拒绝后，在我的派单中点击进去也可以看到拒绝的原因。
        </view>
		<view style="text-align: left;text-indent: 2rem;">6.拼多多等平台的助力任务，如果我的助力人次没达到第三方平台的要求，那么我该怎么办？
		</view>
		<view style="text-align: left;text-indent: 2rem;"> 答：首先，省钱兄平台不保证您发布的助力任务都可以完成哦，像拼多多助力砍价这种是有风险的，如果没有完成那亏损只能自己认了。如果想要任务顺利的完成，可以适当的增加赏金，这样接您任务的人也会更多，增加任务完成的概率。
		</view>
    </view>
</template>

<script>
    export default {
        data() {
            return {}
        },
        methods: {}
    }
</script>

<style>
	page{
		background: #FFFFFF;
	}
</style>
