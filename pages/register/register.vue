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

	.login-box {
		display: flex;
		flex-direction: column;
		margin-top: 100rpx;
	}

	.code-text {
		font-size: 70rpx;
		color: white;
		margin-left: 50rpx;
		margin-bottom: 100rpx;
	}

	.mobile-box {
		height: 100rpx;
		display: flex;
		flex-direction: row;
		align-items: center;
		padding: 0 30rpx;
		border-bottom: 1px solid lightgray;
		margin-top: 20rpx;
	}

	.mobile-prefix {
		color: white;
		font-weight: 400;
		font-size: 38rpx;
		margin-left: 30rpx;
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

	.middle {
		width: 1rpx;
		background-color: #afafb3;
		height: 60rpx;
		margin-left: 20rpx;
	}

	.btn-login {
		background-color: #70d5b3;
		margin-top: 250rpx;
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

	.btn-code {
		width: 220rpx;
		height: 70rpx;
		border-radius: 40rpx;
		display: flex;
		flex-direction: row;
		justify-content: center;
		align-self: center;
		background-color: #70d5b3;
		margin-left: 20rpx;
	}

	.code-btn-text {
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
		<image class="bg" src="/static/images/registerBg.png" :style="{'height': screenHeight + 'px'}">
			<image class="close" src="../../static/images/icon-back.png" @click="close()"></image>
			<view class="login-box">
				<text class="code-text">Sign Up</text>
				<view class="mobile-box">
					<image class="icon" src="../../static/images/user.png">
						<input type="text" class="mobile" :value="username" :model="username" @input="typingUsername"
							placeholder="Username" placeholder-style="color:white" />
				</view>

				<!-- <view class="mobile-box">
					<image class="icon" src="../../static/images/verify.png"></image>
					<input type="text" class="mobile" :value="verifyCode" :model="verifyCode" @input="typingVerifyCode"
						placeholder="VerifyCode" placeholder-style="color:white" style="width: 400rpx" />

					<view class="btn-code" @click="getCode()">
						<text class="code-btn-text">{{codeBtnText}}</text>
					</view>
				</view> -->
				<view class="mobile-box">
					<image class="icon" src="../../static/images/password.png"></image>
					<input type="password" class="mobile" :value="password" :model="password" @input="typingPassword"
						placeholder="Password" placeholder-style="color:white" />
				</view>

				<view class="btn-login" @click="loginOrRegist()">
					<text class="login-btn-text">Join</text>
				</view>
				<text class="login-bottom" @click="goLogin()">Already have an account? Sign In</text>
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
				username: "",
				password: "",
				codeBtnText: "Send",
				codeTimes: 0,
				loginTouched: false,
				codeTouched: false,
			}
		},
		onLoad() {
			this.screenHeight = system.screenHeight;
		},
		onShow() {
			getApp().getUserIsDisable();
		},
		methods: {
			typingUsername(e) {
				var event = e;
				this.username = e.detail.value;
			},
			typingPassword(e) {
				var event = e;
				this.password = e.detail.value;
			},
			getCode() {
				var me = this;
				if (me.codeTimes > 0) {
					return;
				}
				me.codeTouched = true;
				var mobile = me.mobile;
				if (app.isStrEmpty(mobile) || mobile.length != 11) {
					uni.showToast({
						title: "Incorrect phone number",
						icon: "none"
					});
					return;
				}

				var serverUrl = app.globalData.serverUrl;
				// 调用后端发送验证码
				uni.request({
					method: "POST",
					url: serverUrl + "/passport/getSMSCode?mobile=" + mobile,
					success(result) {
						var status = result.data.status;
						if (status != 200) {
							uni.showToast({
								title: result.data.msg,
								icon: "none"
							});
						}

						// 开始倒数60秒限制
						if (me.codeTimes == 0) {
							me.doTimer(59);
						}
					}
				});

			},
			// 发送验证码的倒计时方法
			doTimer(times) {
				var me = this;
				// 倒计时定时器
				var sendCodeBtnFunction = function() {
					var left = times--;
					if (left <= 0) {
						me.codeTouched = false;
						me.codeBtnText = "Send";
						clearInterval(smsTimer);
					} else {
						me.codeBtnText = left + "s";
					}
					me.codeTimes = left;
				};
				var smsTimer = setInterval(sendCodeBtnFunction, 1000);
			},
			loginOrRegist() {
				var me = this;
				var username = me.username;
				var password = me.password;

				if (app.isStrEmpty(username)) {
					uni.showToast({
						title: "Username cannot be empty",
						icon: "none"
					});
					return;
				}
				if (app.isStrEmpty(password)) {
					uni.showToast({
						title: "password cannot be empty",
						icon: "none"
					});
					return;
				}
				var serverUrl = app.globalData.serverUrl;
				//		调用后端注册
				uni.request({
					method: "POST",
					url: serverUrl + "/user/register",
					data: {
						"username": username,
						"password": password
					},
					success(result) {
						console.log(result)
						var code = result.data.code;
						if (code == 200) {
							var userInfo = result.data.data;
							app.setUserInfoSession(userInfo);
							app.setUserSessionToken(userInfo.userToken);
							// 登录成功，关闭当前页
							// me.close();
							uni.reLaunch({
								url: "../loginRegist/loginRegist"
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
			goLogin() {
				uni.navigateTo({
					url: "../loginRegist/loginRegist"
				})
			}
		}
	}
</script>
