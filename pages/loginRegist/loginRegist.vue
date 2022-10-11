<style>
	.page {
		position: absolute;
		left: 0;
		right: 0;
		top: 0;
		bottom: 0;
		display: flex;
		flex-direction: column;
	}

	.bg {
		position: absolute;
		width: 750rpx;
		z-index: -1;
	}

	.close {
		margin: 40rpx;
		width: 50rpx;
		height: 50rpx;
	}
	
	.avatar {
		margin: 100rpx auto;
		width: 300rpx;
		height: 300rpx;
	}

	.login-box {
		display: flex;
		flex-direction: column;
		margin-top: 100rpx;
	}

	.mobile-box {
		height: 100rpx;
		display: flex;
		flex-direction: row;
		align-items: center;
		padding: 0 30rpx;
		border-bottom:1px solid lightgray;
		margin-top: 20rpx;
	}

	.mobile {
		color: white;
		font-weight: 400;
		font-size: 38rpx;
		margin-left: 30rpx;
		width: 600rpx;
	}
	
	.icon {
		width: 60rpx;
		height: 60rpx;
	}

	.btn-login {
		background-color: #70d5b3;
		margin-top: 100rpx;
		width: 750rpx;
		height: 100rpx;
		display: flex;
		flex-direction: row;
		justify-content: center;
	}
	
	.login-btn-text {
		font-size: 40rpx;
		color: #FFFFFF;
		font-weight: 500;
		align-self: center;
	}

	.login-bottom {
		position: absolute;
		font-size: 28rpx;
		bottom: 20rpx;
		flex-direction: row;
		justify-content: center;
		align-self: center;
		color: white;
	}
</style>

<template>
	<view class="page">
		<!-- 这里是状态栏, 每个页面都需要有，目的不让页面覆盖状态栏 -->
		<image class="bg" src="/static/images/loginBg.png" :style="{'height': screenHeight + 'px'}">
			<image class="close" src="../../static/images/icon-close-white.png" @click="close()"></image>
			<image class="avatar" src="../../static/images/avatar.png"></image>
			<view class="login-box">
				<view class="mobile-box">
					<image class="icon" src="../../static/images/user.png"></image>
					<input type="text" class="mobile" :value="mobile" :model="mobile" @input="typingMobile"
						placeholder="用户名" placeholder-style="color:white"/>
				</view>
				<view class="mobile-box">
					<image class="icon" src="../../static/images/password.png"></image>
					<input type="password" class="mobile" :value="verifyCode" :model="verifyCode" @input="typingVerifyCode"
						placeholder="密码" placeholder-style="color:white"/>
				</view>
				<view class="btn-login" @click="loginOrRegist()">
					<text class="login-btn-text">登录</text>
				</view>
				<text class="login-bottom" @click="goRegist()">没有账号?  请注册</text>
			</view>
	</view>
</template>

<script>
	var system = uni.getSystemInfoSync();
	var app = getApp();
	export default {
		data() {
			return {
				screenHeight: 0,
				mobile: "",
				verifyCode: "",
			}
		},
		onLoad() {
			this.screenHeight = system.screenHeight;
		},
		methods: {
			typingMobile(e) {
				var event = e;
				this.mobile = e.detail.value;
			},
			typingVerifyCode(e) {
				var event = e;
				this.verifyCode = e.detail.value;
			},

			// 调用后端登录
			loginOrRegist() {
				var me = this;
				var verifyCode = me.verifyCode;
				var mobile = me.mobile;
				if (app.isStrEmpty(mobile)) {
					uni.showToast({
						title: "请输入用户名",
						icon: "none"
					});
					return;
				}
				if (app.isStrEmpty(verifyCode)) {
					uni.showToast({
						title: "请输入密码",
						icon: "none"
					});
					return;
				}
				var serverUrl = app.globalData.serverUrl;
				uni.request({
					method: "POST",
					url: serverUrl + "/user/login",
					data: {
						"username": mobile,
						"password": verifyCode
					},
					success(result) {
						var code = result.data.code;
						if (code == 200) {
							var userInfo = result.data.data;
							app.setUserInfoSession(userInfo.users);
							app.setUserSessionToken(userInfo.token);
							uni.reLaunch({
								url: "../index/index"
							})
						} else if (code != 200) {
							uni.showToast({
								title: result.data.message,
								icon: "none",
								duration: 3000
							});
						}
					}
				});
			},
			close() {
				uni.navigateBack({
					delta: 1,
				})
			},
			goRegist(){
				uni.navigateTo({
					url: "../register/register"
				})
			}
		}
	}
</script>
