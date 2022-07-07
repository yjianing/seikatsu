<script>
	export default {
		onLaunch: function() {
			console.log('App Launch')
			let _this = this;
			uni.getLocation({
				wgs84: false, // 传入 true 会返回高度信息，由于获取高度需要较高精确度，会减慢接口返回速度
				sHighAccuracy: false, // 开启高精度定位
				highAccuracyExpireTime: 3000, //高精度定位超时时间(ms)，指定时间内返回最高精度，该值3000ms以上高精度定位才有效果
				success: function(res) {
					console.log('成功获取位置信息', res)
					uni.setStorage({
						key: 'res',
						data: res
					})
				},
				fail: function(error) {
					console.log('获取当前位置失败', error)
				},
				complete: function(com) {
					console.log('接口调用结束的回调函数（调用成功、失败都会执行）', com)
				}
			});
		},
		onShow: function() {
			console.log('App Show')
		},
		onHide: function() {
			console.log('App Hide')
		}
	}
</script>

<style>
	/*每个页面公共css */
</style>
