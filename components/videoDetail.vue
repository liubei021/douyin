<style>
	.icon {
		width: 80rpx;
		height: 80rpx;
		opacity: 0.9;
	}

	.user-face {
		width: 100rpx;
		height: 100rpx;
		border-radius: 100rpx;
	}

	.play-cd {
		width: 120rpx;
		height: 120rpx;
		opacity: 0.8;
	}

	.refresh-info-txt {
		color: #F1F1F1;
		text-align: center;
		font-size: 12px;
	}

	.full-screen-btn {
		width: 230rpx;
		background: #323232;
		padding: 10rpx 0;
		border-radius: 50rpx;
		display: flex;
		flex-direction: row;
		justify-content: center;
		margin-bottom: 15rpx;
	}

	.full-screen-img {
		width: 35rpx;
		height: 35rpx;
		margin-right: 5rpx;
	}

	.publish-info-box {
		position: absolute;
		bottom: 200rpx;
		left: 0;
		right: 0;
		padding-left: 20rpx;
		padding-right: 20rpx;
		flex-direction: row;
		justify-content: space-between;
		align-items: center;
	}

	.publish-info-vloger-name {
		color: #FFFFFF;
		font-size: 40rpx;
		font-weight: 600;
		padding: 10rpx;
	}

	.publish-info-music-box {
		flex-direction: row;
		align-items: center;
	}

	.publish-info-content {
		color: #FFFFFF;
		font-size: 28rpx;
		font-weight: 400;
		padding: 10rpx;
		lines: 5;
		width: 520rpx;
		text-overflow: ellipsis;
	}

	.icon-fire {
		width: 36rpx;
		height: 36rpx;
	}

	.muisc-words {
		color: #FFFFFF;
		font-size: 28rpx;
		padding: 10rpx;
		width: 400rpx;
	}

	.some-counts {
		font-size: 24rpx;
		font-weight: 500;
		text-align: center;
		color: #FFFFFF;
		margin-top: 2rpx;
	}

	.operation-box {
		position: absolute;
		top: 0;
		bottom: 0;
		right: 0;
		align-items: center;
		justify-content: center;
		padding-right: 20rpx;
	}

	.operation-face-box {
		border-radius: 100rpx;
		border-color: #FFFFFF;
		border-width: 3rpx;
	}

	.follow-me {
		width: 40rpx;
		height: 40rpx;
		border-radius: 10px;
		position: relative;
		top: -20rpx;
	}

	.like-box {
		flex-direction: column;
		align-items: center;
		margin-top: 30rpx;
	}

	.comment-and-share-box {
		flex-direction: column;
		align-items: center;
		margin-top: 45rpx;
	}
</style>

<template>
	<view style="flex: 1;">
		<!-- <uni-list @change="onchange" :num="playerList.length"> -->
		<list :pagingEnabled="true" :show-scrollbar="false" @scroll="listScroll" @scrollend="scroll" :scrollable="true">
			<cell :recycle="false" v-for="(item, index) in playerList" :key="index" :data-index="index"
				:style="{'height': screenHeight + 'px'}">
				<!-- <uni-video :src="item.url" :playStatus="playStatus" :screenHeight="screenHeight" v-if="playerCur === index" @play="onplay"></uni-video> -->
				<video v-if="playerCur === index" ref="videoDetail" id="videoDetail" :src="item.url"
					:object-fit="item.width >= item.height ? 'contain' : 'fill'" :controls="controlStatus"
					:enable-progress-gesture="false" :show-play-btn="false" :vslide-gesture-in-fullscreen="false"
					:show-progress="false" loop autoplay show-loading="true" http-cache="true" style="width: 750rpx;"
					:style="{height: screenHeight + 'px'}" @click="playOrPause" @play="onplay" @error="onerror"
					@fullscreenchange="fullscreenchange" @timeupdate="timeupdate"></video>
				<image :lazy-load="true" :fade-show="false" v-if="!item.play" :src="item.cover"
					:mode="item.width >= item.height ? 'aspectFit' : 'scaleToFill'"
					style="width: 750rpx; filter: blur(10px);" :style="{height: screenHeight+ 'px'}"></image>
				<!--<image :lazy-load="true" :fade-show="false" v-if="!item.play" :src="item.cover" mode="" style="width: 750rpx;position:absolute;left: 0;right: 0;top: 0;bottom: 0; filter: blur(10px);" :style="{height: screenHeight+ 'px'}"></image>-->
			</cell>
		</list>
		<!-- </uni-list> -->

	</view>
</template>

<script>
	var app = getApp();
	export default {
		props: {
			screenHeight: {
				default: 0
			},
			src: {
				default: false
			},
			playStatus: {
				default: false
			},
			vlog: {
				default: false
			},
		},
		data() {
			return {
				thisVlog: {}, // 当前的短视频对象
				thisVlogId: "", // 当前的短视频主键id
				thisVlogerId: "", // 当前的短视频博主的主键id
				userId: "",

				playerCur: 0,
				page: 0,
				totalPage: 0,
				playerList: this.videoList,
				thisVlogTotalComentCounts: 0,

				videoContext: {},

				currentIndex: 0,
				contentOffsetY: 0,

				times: new Date().getTime(),

				objectFit: "fill",
				isIOS: uni.getSystemInfoSync().platform == "ios",
				cdStatus: false,
				controlStatus: false,
			}
		},
		created() {

			if (!this.isIOS) {
				this.objectFit = "cover";
			}

			// 查询首页短视频列表
			// this.displayVideoPaging(this.page + 1, true);
			
			var playerList = [];
			playerList.push(this.vlog);
			this.playerList = playerList;
			// console.log(this.playerList)

			var videoContext = uni.createVideoContext('videoDetail');
			this.videoContext = videoContext;
		},
		watch: {
			refreshList(value) {
				var me = this;
				var newList = value;
				if (newList != null && newList != undefined && newList.length > 0) {
					me.playerList = newList;
				}

				// 重置
				this.playerCur = 0;
				this.currentIndex = 0;
				this.contentOffsetY = 0;
			},
			playStatus: function(val) {
				var me = this;

				if (!val) {
					me.videoContext.pause();
				} else {
					me.videoContext.play();
				}
			}
		},
		methods: {

			goUserInfoSeeSee(userId) {
				uni.setStorageSync("userPageId", userId);
				uni.navigateTo({
					url: "/pages/me/vlogerInfo?userPageId=" + userId
				})
			},

			listScroll(e) {},

			downloadVlog() {

				var me = this;
				var serverUrl = app.globalData.serverUrl;
				var currentIndex = me.playerCur;
				var vlog = me.playerList[currentIndex];
				var pendingLength = vlog.url;

			},

			onplay: function(e) {
				this.cdStatus = true
				if (uni.getSystemInfoSync().platform == 'ios') {
					this.doplay(0.1);
				}
			},
			onerror: function(err) {},
			timeupdate: function(e) {
				if (e.detail.currentTime > 0.2) {
					this.doplay(e.detail.currentTime);
				}
			},

			playOrPause() {
				var me = this;
				var playStatus = this.playStatus;
				var cdStatus = this.cdStatus;

				if (!playStatus) {
					me.videoContext.pause();
				} else {
					me.videoContext.play();
				}
				this.playStatus = !playStatus;
				this.cdStatus = !cdStatus;
			},


			scroll: function(e) {},

			displayVideoPaging(page, needClearList) {
				// 查询首页短视频列表
				var me = this;
				var vlogId = me.vlogId;

				var myUserInfo = getApp().getUserInfoSession();
				var userId = "";
				if (myUserInfo != null) {
					userId = myUserInfo.id;
				}

				var serverUrl = app.globalData.serverUrl;
				uni.request({
					method: "GET",
					url: serverUrl + "/vlog/detail?userId=" + userId + "&vlogId=" + vlogId,
					success(result) {
						// console.log(result)
						if (result.data.status == 200) {
							var vlog = result.data.data;
							var playerList = [];
							playerList.push(vlog);
							me.playerList = playerList;
							me.freshCommentCounts();
							me.setThisVlogInfo();
						} else {
							uni.showToast({
								title: result.data.msg,
								icon: "none",
								duration: 3000
							});
						}

					}
				});
			},

			doplay: function(time) {
				if (time > 0) {
					this.playerList[this.playerCur].play = true;
				}
			},
			onchange: function(index) {
				if (index != this.playerCur) {
					this.playerList[this.playerCur].play = false;
					this.playerCur = index;
				}

				this.setThisVlogInfo();
			},

			// 设置当前vlog的信息
			setThisVlogInfo() {
				var me = this;
				var serverUrl = app.globalData.serverUrl;

				if (me.playerList != null && me.playerList != undefined && me.playerList.length > 0) {
					var currentIndex = me.playerCur;
					var vlog = me.playerList[currentIndex];
					this.thisVlog = vlog;
					this.thisVlogId = vlog.vlogId;
					this.thisVlogerId = vlog.vlogerId;
				}
			},
			
			commentToggle() {
				this.$refs.comment.open();
				uni.hideTabBar({
					animation: true
				});
			},
			shareToggle() {
				this.$refs.share.open();
				uni.hideTabBar({
					animation: true
				});
			},
			fullScreen(type) {
				this.videoContext.requestFullScreen({
					direction: 90
				});
				this.controlStatus = true
			},
			fullscreenchange(e) {
				if (!e.detail.fullScreen) {
					this.controlStatus = false
				}
			}
		}
	}
</script>
