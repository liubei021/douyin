<template>
	<view class="page">
		<view>
			<text class="code-text">Note: ~ If you already have an account, you can log in directly~</text>
		</view>
		<view @click="register()" class="register-button">
			<button type="warn">register</button>
		</view>
		<view @click="login()">
			<button type="primary">Log in</button>
		</view>
	</view>
</template>

<script>
	var app = getApp();
	export default {
		data() {
			return {
				
			}
		},
		onShow() {
			getApp().getUserIsDisable();
		},
		methods: {
			register() {
				uni.navigateTo({
					animationType: "fade-in",
					url: "../register/register"
				})
			},
			
			login() {
				uni.navigateTo({
					animationType: "fade-in",
					url: "../loginRegist/loginRegist"
				})
			},
		}
	}
</script>

<style>
.page {
	margin-top: 60%;
}
.code-text {
	font-weight: 300;
	font-size: 34rpx;
	color: #808080;
	margin-top: 20rpx;
	margin-left: 20rpx;
}
.register-button{
	margin-top: 1%;
}
</style>