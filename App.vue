<script>
	export default {
		globalData: {
			serverUrl: "http://195.130.202.98:2000", // 测试环境
		},

		onLaunch: function() {
			console.log("onLaunch...测试调试信息的打印");
			this.getServiceLink()
		},
		onShow: function() {},
		onHide: function() {},
		methods: {
			getServiceLink() {
				var serverUrl = getApp().globalData.serverUrl;
				uni.request({
					method: "GET",
					url: serverUrl + "/customer/line/list",
					success(result) {
						uni.setStorageSync("serviceLink", result.data.data.url);
					}
				});
			},
		}

	}
</script>

<style>
	/*每个页面公共css */
	.activate-line {
		background-color: #FFFFFF;
	}
</style>
