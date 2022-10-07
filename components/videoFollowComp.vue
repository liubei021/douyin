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

	.toast-box {
		position: fixed;
		border-radius: 10px;
		width: 130rpx;
		height: 130rpx;
		display: flex;
		flex-direction: row;
		justify-content: center;
		top: 480rpx;
		left: 200rpx;
	}

	.toast-bg {
		width: 130rpx;
		height: 130rpx;
		position: absolute;
		opacity: 0.9;
	}

	.toast-text {
		color: white;
		font-size: 50rpx;
		font-weight: bold;
		line-height: 130rpx;
	}
</style>

<template>
	<view style="flex: 1;">
		<!-- <uni-list @change="onchange" :num="playerList.length"> -->
		<list :pagingEnabled="true" :show-scrollbar="false" @scroll="listScroll" @scrollend="scroll" :scrollable="true">
			<refresh @pullingdown="onpullingdown" @refresh="onrefresh" :display="refreshing ? 'show' : 'hide'">
				<text class="refresh-info-txt"></text>
				<loading-indicator></loading-indicator>
			</refresh>
			<cell :recycle="false" v-for="(item, index) in playerList" :key="index" :data-index="index"
				:ref="'item'+index" :style="{'height': screenHeight + 'px'}">
				<!-- <uni-video :src="item.url" :playStatus="playStatus" :screenHeight="screenHeight" v-if="playerCur === index" @play="onplay"></uni-video> -->
				<video v-if="playerCur === index" ref="myFollowVideo" id="myFollowVideo" :src="item.url"
					:object-fit="item.width >= item.height ? 'contain' : 'fill'" :controls="controlStatus"
					:enable-progress-gesture="false" :show-play-btn="false" :vslide-gesture-in-fullscreen="false"
					:show-progress="false" loop :autoplay='isAuto()' show-loading="true" http-cache="true"
					style="width: 750rpx;" :style="{height: screenHeight + 'px'}" @click="playOrPause" @play="onplay"
					@error="onerror" @ended="onended" @fullscreenchange="fullscreenchange"
					@timeupdate="timeupdate"></video>
				<image :lazy-load="true" :fade-show="false" v-if="!item.play" :src="item.cover"
					:mode="item.width >= item.height ? 'aspectFit' : 'scaleToFill'"
					style="width: 750rpx; filter: blur(10px);" :style="{height: screenHeight+ 'px'}"></image>
				<!--<image :lazy-load="true" :fade-show="false" v-if="!item.play" :src="item.cover" mode="" style="width: 750rpx;position:absolute;left: 0;right: 0;top: 0;bottom: 0; filter: blur(10px);" :style="{height: screenHeight+ 'px'}"></image>-->
				<view class="publish-info-box">
					<view class="">
						<view class="full-screen-btn" @click="fullScreen()">
							<image class="full-screen-img" src="/static/images/fullscreen.png"></image>
							<text style="color:white;font-size: 28rpx;">full screen</text>
						</view>
						<text class="publish-info-vloger-name">@{{item.vlogerName}}</text>
						<text class="publish-info-content">{{item.content}}</text>
						<view class="publish-info-music-box">
							<text class="muisc-words">{{item.vlogerName}} original sound creation</text>
						</view>
					</view>
					<view class="" style="flex-direction: row;">
						<image v-if="cdStatus" src="/static/images/cd-play.gif" class="play-cd"></image>
						<image v-else src="/static/images/icon-cd.png" class="play-cd"></image>
						<!--<image v-if="isIOS"
							:src="'https://imooc-news.oss-cn-shanghai.aliyuncs.com/image/cd-play-4.gif?time='+times"
							class="play-cd"></image> -->
					</view>
				</view>
				<!-- 视频展示右侧的操作按钮，头像 - 点赞 - 评论 - 转发 -->
				<view class="operation-box">
					<view class="operation-face-box">
						<image :src="item.vlogerFace" class="user-face" @click="goUserInfoSeeSee(item.vlogerId)">
						</image>
					</view>
					<image v-if="!item.doIFollowVloger" src="/static/images/icon-follow.png"
						@click="followMe(item.vlogerId)" class="follow-me"></image>
					<view class="like-box">
						<image v-if="!item.doILikeThisVlog" src="/static/images/icon-unlike.png"
							@click="likeOrDislikeVlog(1)" class="icon"></image>
						<image v-if="item.doILikeThisVlog" src="/static/images/icon-like.png"
							@click="likeOrDislikeVlog(0)" class="icon"></image>
						<text class="some-counts">{{item.likeCounts}}</text>
					</view>
					<view class="comment-and-share-box">
						<image src="/static/images/icon-comments.png" @click="commentToggle" class="icon"></image>
						<!-- <text class="some-counts">{{item.commentsCounts}}</text> -->
						<text class="some-counts">{{thisVlogTotalComentCounts}}</text>
					</view>
					<view class="comment-and-share-box">
						<image src="/static/images/icon-share.png" @click="shareToggle" class="icon"></image>
						<text class="some-counts">share</text>
					</view>
				</view>
			</cell>
		</list>
		<!-- </uni-list> -->

		<view v-if="thisVlog != null && thisVlog != {}">
			<!-- 底部评论窗口popup -->
			<uni-popup ref="comment" type="comment">
				<uni-popup-comments :thisVlogerId="thisVlogerId" :thisVlogId="thisVlogId"></uni-popup-comments>
			</uni-popup>
			<uni-popup ref="share" background-color="#fff" type="share">
				<uni-popup-share :thisVlogerId="thisVlogerId" :thisVlogId="thisVlogId" :vlogUrl="thisVlog.url"
					:isPrivate="thisVlog.isPrivate"></uni-popup-share>
			</uni-popup>
		</view>

		<view v-if="toastStatus" class="toast-box">
			<image class="toast-bg" src="/static/images/point.png">
				<text class="toast-text">+10</text>
		</view>

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
			playFollowStatus: {
				default: false
			},
			playStatus: {
				default: false
			},
			videoList: {
				default: []
			},
			refreshList: {
				default: []
			},
			pagingList: {
				default: []
			},
			count: {
				default: 0
			},
			exitClose: {
				default: 0
			}
		},
		data() {
			return {
				thisVlog: {}, // 当前的短视频对象
				thisVlogId: "", // 当前的短视频主键id
				thisVlogerId: "", // 当前的短视频博主的主键id

				refreshing: false,
				showRefreshLoading: "hide",

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
				toastStatus: false,
				controlStatus: false,
			}
		},
		created() {
			if (!this.isIOS) {
				this.objectFit = "cover";
			}

			// 查询首页短视频列表
			this.displayVideoPaging(this.page + 1, true);

			var videoContext = uni.createVideoContext('myFollowVideo');
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

			playFollowStatus: function(val) {
				var me = this;
				if (!val) {
					me.videoContext.pause();
				} else {
					me.videoContext.play();
				}
			},

			count(num) {

				this.displayVideoPaging(1, true);
			},
			exitClose(num) {
				this.playFollowStatus = false
			},
			playerCur(val) {
				if (getApp().getUserIsPlay() == 0) {
					this.playFollowStatus = true
				}
			}
		},
		methods: {
			isAuto() {
				return getApp().getUserIsPlay() == 0 && this.playFollowStatus ? true : false
			},
			freshCommentCounts() {
				var me = this;
				var userId = getApp().getUserInfoSession().id;
				var serverUrl = app.globalData.serverUrl;


				if (me.playerList == null || me.playerList == undefined || me.playerList.length == 0) {
					return;
				}
				var currentIndex = me.playerCur;
				var vlog = me.playerList[currentIndex];
				var vlogId = vlog.vlogId;

				var serverUrl = app.globalData.serverUrl;
				uni.request({
					method: "GET",
					url: serverUrl + "/comment/counts?vlogId=" + vlogId,
					success(result) {

						if (result.data.status == 200) {
							me.thisVlogTotalComentCounts = result.data.data;
						} else {
							me.thisVlogTotalComentCounts = 0;
						}
					}
				});
			},
			likeOrDislikeVlog(isLike) {
				var me = this;
				if (isLike == 1) {
					// 喜欢/点赞视频

					var myUserInfo = getApp().getUserInfoSession();
					if (myUserInfo == null) {
						uni.showToast({
							duration: 3000,
							title: "please sign in~",
							icon: "none"
						});
						uni.navigateTo({
							url: "../loginRegist/loginRegist",
							animationType: "slide-in-bottom",
							success() {
								me.loginWords = "please sign in"
							}
						});
						return;
					}

					var userId = getApp().getUserInfoSession().id;
					var serverUrl = app.globalData.serverUrl;
					var currentIndex = me.playerCur;
					var vlog = me.playerList[currentIndex];

					uni.request({
						method: "POST",
						header: {
							headerUserId: userId,
							headerUserToken: app.getUserSessionToken()
						},
						url: serverUrl + "/vlog/like?userId=" + userId + "&vlogerId=" + vlog.vlogerId +
							"&vlogId=" + vlog.vlogId,
						success(result) {

							if (result.data.status == 200) {
								me.reLikePlayList(vlog.vlogId);
								me.refreshVlogCounts();
							} else {
								uni.showToast({
									title: result.data.msg,
									icon: "none",
									duration: 3000
								});
							}
						}
					});

				} else if (isLike == 0) {
					// 取消喜欢/点赞视频

					var myUserInfo = getApp().getUserInfoSession();
					if (myUserInfo == null) {
						uni.showToast({
							duration: 3000,
							title: "please sign in~",
							icon: "none"
						});
						uni.navigateTo({
							url: "../loginRegist/loginRegist",
							animationType: "slide-in-bottom",
							success() {
								me.loginWords = "please sign in"
							}
						});
						return;
					}

					var userId = getApp().getUserInfoSession().id;
					var serverUrl = app.globalData.serverUrl;
					var currentIndex = me.playerCur;
					var vlog = me.playerList[currentIndex];

					uni.request({
						method: "POST",
						header: {
							headerUserId: userId,
							headerUserToken: app.getUserSessionToken()
						},
						url: serverUrl + "/vlog/unlike?userId=" + userId + "&vlogerId=" + vlog.vlogerId +
							"&vlogId=" + vlog.vlogId,
						success(result) {

							if (result.data.status == 200) {
								me.reDislikePlayList(vlog.vlogId);
								me.refreshVlogCounts();
							} else {
								uni.showToast({
									title: result.data.msg,
									icon: "none",
									duration: 3000
								});
							}
						}
					});

				}
			},

			// 喜欢/点赞的list重新设置
			reLikePlayList(vlogId) {
				var me = this;
				var playerList = me.playerList;
				// 关注以后，循环当前playerList，修改对应粉丝关系的doIFollowVloger改为true
				for (var i = 0; i < playerList.length; i++) {
					var vlog = playerList[i];
					if (vlog.vlogId == vlogId) {
						vlog.doILikeThisVlog = true;
						playerList.splice(i, 1, vlog);
					}
				}
				me.playerList = playerList;
			},
			reDislikePlayList(vlogId) {
				var me = this;
				var playerList = me.playerList;
				// 关注以后，循环当前playerList，修改对应粉丝关系的doIFollowVloger改为true
				for (var i = 0; i < playerList.length; i++) {
					var vlog = playerList[i];
					if (vlog.vlogId == vlogId) {
						vlog.doILikeThisVlog = false;
						playerList.splice(i, 1, vlog);
					}
				}
				me.playerList = playerList;
			},

			// 关注后的list重新设置
			reFollowPlayList(vlogerId) {
				var me = this;
				var playerList = me.playerList;

				// 关注以后，循环当前playerList，修改对应粉丝关系的doIFollowVloger改为true
				for (var i = 0; i < playerList.length; i++) {
					var vlog = playerList[i];
					if (vlog.vlogerId == vlogerId) {
						vlog.doIFollowVloger = true;
						playerList.splice(i, 1, vlog);
					}
				}
				me.playerList = playerList;
			},
			// 取关后的list重新设置
			reCancelPlayList(vlogerId) {
				var me = this;
				var playerList = me.playerList;

				// 关注以后，循环当前playerList，修改对应粉丝关系的doIFollowVloger改为true
				for (var i = 0; i < playerList.length; i++) {
					var vlog = playerList[i];
					if (vlog.vlogerId == vlogerId) {
						vlog.doIFollowVloger = false;
						playerList.splice(i, 1, vlog);
					}
				}
				me.playerList = playerList;
			},


			// 关注博主
			followMe(vlogerId) {
				var me = this;
				var myUserInfo = getApp().getUserInfoSession();
				if (myUserInfo == null) {
					uni.showToast({
						duration: 3000,
						title: "please sign in~",
						icon: "none"
					});

					uni.navigateTo({
						url: "../loginRegist/loginRegist",
						animationType: "slide-in-bottom",
						success() {
							me.loginWords = "please sign in"
						}
					});

					return;
				}

				var userId = getApp().getUserInfoSession().id;
				var serverUrl = app.globalData.serverUrl;
				uni.request({
					method: "POST",
					header: {
						headerUserId: userId,
						headerUserToken: app.getUserSessionToken()
					},
					url: serverUrl + "/fans/follow?myId=" + userId + "&vlogerId=" + vlogerId,
					success(result) {

						if (result.data.status == 200) {
							me.reFollowPlayList(vlogerId);
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

			goUserInfoSeeSee(userId) {
				uni.setStorageSync("userPageId", userId);
				uni.navigateTo({
					url: "/pages/me/vlogerInfo?userPageId=" + userId
				})
			},

			listScroll(e) {
				if (e.contentOffset.y > 0) {
					this.$emit("showLoading");
				}
			},

			downloadVlog() {

				var me = this;
				var serverUrl = app.globalData.serverUrl;
				var currentIndex = me.playerCur;
				var vlog = me.playerList[currentIndex];
				var pendingLength = vlog.url;

			},

			copyLink() {
				var me = me;
			},

			showQRCode() {
				var me = me;
			},

			changeVlogToPrivate() {
				var me = me;
			},

			onrefresh(e) {
				this.refreshing = true;
				setTimeout(() => {
					this.refreshing = false;
					this.$emit("hideLoading");
					this.refreshText = '↓ Pull To Refresh'
				}, 300)

				// 通过list组件的下拉刷新触发当前所在页面的下拉刷新
				uni.startPullDownRefresh();
			},

			onplay: function(e) {
				this.playFollowStatus = true
				this.cdStatus = true
				if (uni.getSystemInfoSync().platform == 'ios') {
					// this.$emit("play", 0.1);
					this.doplay(0.1);
				}
			},
			onerror: function(err) {},
			onended(e) {
				let _this = this
				if (getApp().getUserIsPlay() == 0 && this.$refs[`item${this.playerCur+1}`]) {
					const dom = uni.requireNativePlugin('dom')
					const el = this.$refs[`item${this.playerCur+1}`][0]
					dom.scrollToElement(el, {})

					const remainWatchNum = JSON.parse(uni.getStorageSync("userInfo")).remainWatchNum
					if (remainWatchNum > 0) {
						var userId = getApp().getUserInfoSession().id;
						var serverUrl = app.globalData.serverUrl;
						uni.request({
							method: "POST",
							header: {
								headerUserId: userId,
								headerUserToken: app.getUserSessionToken()
							},
							url: serverUrl + "/flow/insertPoints",
							data: JSON.stringify({
								points: 10,
								userId: userId
							}),
							success(result) {
								if (result.data.status == 200) {
									_this.toastStatus = true
									setTimeout(() => {
										_this.toastStatus = false
									}, 1000)
								}
							}
						});
					}
				}
			},
			timeupdate: function(e) {
				if (e.detail.currentTime > 0.2) {
					// this.$emit("play", e.detail.currentTime);
					this.doplay(e.detail.currentTime);
				}
			},

			playOrPause() {
				var me = this;
				var playFollowStatus = this.playFollowStatus;
				var cdStatus = this.cdStatus;

				if (!playFollowStatus) {
					me.videoContext.pause();
				} else {
					me.videoContext.play();
				}
				this.playFollowStatus = !playFollowStatus;
				this.cdStatus = !cdStatus;
			},


			scroll: function(e) {
				this.cdStatus = false;
				let originalIndex = this.currentIndex;
				let isNext = false;
				if (e.contentOffset.y < this.contentOffsetY) {
					isNext = true;
				}
				this.contentOffsetY = e.contentOffset.y;

				var num = this.playerList.length;

				this.currentIndex = Math.round(Math.abs(this.contentOffsetY) / (e.contentSize.height / num));
				this.onchange(this.currentIndex);

				this.times = new Date().getTime();

				// 判断如果视频列表总长度-当前下标，少于3个，则开始分页查询后续的视频，并且追加到现有list中
				if ((this.playerList.length - this.currentIndex) < 3) {
					// 如果要分页的page和总数totalPage相等，则没有更多
					if (this.page == this.totalPage) {
						return;
					}
					this.displayVideoPaging(this.page + 1, false);
				}

			},

			// 分页查询新的list，并且追加到现有list中
			displayVideoPaging(page, needClearList) {

				// 查询首页短视频列表
				var me = this;
				var myUserInfo = getApp().getUserInfoSession();
				var userId = "";
				if (myUserInfo != null) {
					userId = myUserInfo.id;
				} else {
					return;
				}
				var serverUrl = app.globalData.serverUrl;
				uni.request({
					method: "GET",
					header: {
						headerUserId: userId,
						headerUserToken: app.getUserSessionToken()
					},
					url: serverUrl + "/vlog/followList?myId=" + userId + "&page=" + page + "&pageSize=10",
					success(result) {

						if (result.data.status == 200) {
							var vlogList = result.data.data.rows;
							var totalPage = result.data.data.total;
							if (needClearList) {
								me.playerList = [];
							}
							me.playerList = me.playerList.concat(vlogList);
							me.page = page;
							me.totalPage = totalPage;

							if (needClearList) {
								me.setThisVlogInfo();
								me.freshCommentCounts();
							}
							// me.doTimer();
						} else {
							uni.showToast({
								title: result.data.msg,
								icon: "none",
								duration: 3000
							});
						}

					},
					complete() {
						// me.doTimer();
					}
				});
			},

			doTimer() {
				var me = this;
				var t = setTimeout(() => {

					if (me.playerList != null && me.playerList != undefined && me.playerList.length > 0) {
						me.videoContext.pause();
						me.playFollowStatus = false;
					}
				}, 3000)
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

				this.refreshVlogCounts();
				this.setThisVlogInfo();
				this.freshCommentCounts();
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

			refreshVlogCounts() {
				// 查询当前点赞数，重新赋值给当前视频
				var me = this;
				var serverUrl = app.globalData.serverUrl;
				var currentIndex = me.playerCur;
				var vlog = me.playerList[currentIndex];
				uni.request({
					method: "POST",
					url: serverUrl + "/vlog/totalLikedCounts?vlogId=" + vlog.vlogId,
					success(result) {
						if (result.data.status == 200) {
							var counts = result.data.data;
							me.reChangeVlogLikedCountsInPlayList(vlog.vlogId, counts);
						}
					}
				});
			},

			reChangeVlogLikedCountsInPlayList(vlogId, counts) {
				var me = this;
				var playerList = me.playerList;
				// 关注以后，循环当前playerList，修改对应粉丝关系的doIFollowVloger改为true
				for (var i = 0; i < playerList.length; i++) {
					var vlog = playerList[i];
					if (vlog.vlogId == vlogId) {
						vlog.likeCounts = counts;
						playerList.splice(i, 1, vlog);
					}
				}
				me.playerList = playerList;
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
