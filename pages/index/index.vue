<template>
	<view class="content">
		<!-- 定位显示区 -->
		<iLocation :addressName='addressName'></iLocation>
		<!-- 内容显示区 -->
		<view class="content-list">
			<!-- 按钮 -->
			<view class="button">
				<view :class="['abc', inv==0?'drink':'']" @click="inv=0, getPois(1)">
					吃
				</view>
				<view :class="['abc', inv==1?'drink':'']" @click="inv=1, getPois(2)">
					喝
				</view>
				<view :class="['abc', inv==2?'drink':'']" @click="inv=2, getPois(3)">
					玩
				</view>
				<view :class="['abc', inv==3?'drink':'']" @click="inv=3, getPois(4)">
					购
				</view>
			</view>
			<!-- 数据显示区 -->
			<view class="content-data">
				<view :class="['vIf', theme ? '' : 'none']">
					请选择主题
				</view>
				<scroll-view :scroll-top="scrollTop" scroll-y="true" class="scroll-y">

					<view class="list">
						<view class="item" v-for="poi in showPois" :key="poi.id">
							<text class="name-style" :showPois='showPois'>{{ poi.name }}</text>
						</view>
					</view>
				</scroll-view>
				<view class="select">
					<view class="select-list">
						<view :class="['select-button', abc==1?'sel-but-drink':'']" @click="select(5), abc=1">
							5
						</view>
						<view :class="['select-button', abc==2?'sel-but-drink':'']" @click="select(10), abc=2">
							10
						</view>
						<view :class="['select-button', abc==3?'sel-but-drink':'']" @click="select(15), abc=3">
							15
						</view>
						<view :class="['select-button', abc==4?'sel-but-drink':'']" @click="select(20), abc=4">
							20
						</view>
					</view>
				</view>
			</view>
		</view>
		<!-- 按键显示区 -->
		<view>
			<!-- 按键 -->
			<view class="but">
				<text>不知道如何选择？</text>
				<button class="screen" type="default" @click="but">
					筛选
				</button>
				<text>点击按钮帮你选择</text>
			</view>
			<!-- 筛选结果显示区 -->
			<view class="display">
				{{showResult}}
			</view>
		</view>
	</view>
</template>

<script>
	import iLocation from '../Location/Location.vue'
	import amap from '../../js_Sdk/amap-wx.js'
	export default {
		data() {
			return {
				amapPlugin: null, //初始key
				markersData: { //获取到的经纬度和key
					latitude: '',
					longitude: '',
					key: "bbc7a1eca35bad0600a586fd56b29f9d"
				},
				addressName: '', //详细地址
				inv: null, // 默认第一个按钮
				searchPoiType: "", //根据类型搜索poiß
				searchPoiKw: "", //根据关键词搜索poi
				searchPois: [], //存储获取的poi数据
				showPois: [], //用于显示的poi
				activeTheme: 1, //默认激活第一个主题
				poiType: { // 所有选择主题
					1: "050100|050200|050300|050400|050800|050900", //吃相关
					2: "050500|050600|050700", //喝相关
					3: "080100|080300|080400|080500|080600", //休闲娱乐
					4: "060100|060200|060400|060900|061000|061400" //购物
				},
				activeLimit: 20, // 显示的个数
				showResult: '', // 筛选的结果,
				abc: 4, //判断用户选择的是那个
				theme: true,
			}
		},
		components: {
			iLocation
		},
		// 分享给朋友
		onShareAppMessage(res) {
			if (res.from === 'button') {
				console.log(res.target)
			}
			return {
				title: '分享去哪·吃喝玩乐小程序',
				path: '/pages/index/index'
			}
		},
		//分享到朋友圈
		onShareTimeline(res) {
			let distSource = uni.getStorageSync('distSource');
			if (distSource) {
				return {
					title: '欢迎使用xxx商城',
					type: 0,
					query: 'id=' + distSource,
					summary: "",
					imageUrl: "https://58d.oss-cn-hangzhou.aliyuncs.com/goods/ttg_1596073788000.png"
				}
			}
		},
		onLoad() {
			// 获取经纬信息并保存
			let _this = this
			uni.getStorage({
				key: 'res',
				success(e) {
					_this.markersData.latitude = e.data.latitude
					_this.markersData.longitude = e.data.longitude
				}
			})
			// 初始key
			this.amapPlugin = new amap.AMapWX({
				key: _this.markersData.key
			});
			//默认初始化poi搜索的类型为吃
			this.searchPoiType = this.poiType[this.activeTheme]
			// 获取详细地址信息
			uni.showLoading({
				title: '获取信息中'
			});
			this.amapPlugin.getRegeo({
				success: (data) => {
					console.log(data)
					this.addressName = data[0].name;
					uni.hideLoading();
				}
			});
		},
		methods: {
			// 按钮点击切换主题并重新获取数据
			getPois(index) {
				this.theme = false
				console.log(this.theme)
				this.activeTheme = index
				this.searchPoiType = this.poiType[this.activeTheme]
				uni.showLoading({
					title: '加载中'
				});
				setTimeout(function() {
					uni.hideLoading();
				}, 100);
				this.getPoiInfoAround()
			},
			// 获取周边
			getPoiInfoAround() {
				uni.showLoading({
					title: '获取数据中'
				});
				this.amapPlugin.getPoiAround({
					"querytypes": this.searchPoiType,
					"querykeywords": this.searchPoiKw,
					success: (data) => {
						//成功回调
						console.log(1)
						this.searchPois = data.poisData
						this.setShowPois()
						uni.hideLoading();
						console.log("poi", this.searchPois)
					},
					fail: (info) => {

					}
				})
			},
			// 将获取的数据储存到showPois中
			setShowPois() {
				this.showPois = this.searchPois.slice(0, this.activeLimit)
			},
			// 选择数据显示的数据
			select(index) {
				this.activeLimit = index
				this.showPois = this.searchPois.slice(0, this.activeLimit)
			},
			// 开始筛选
			but() {
				if (this.showPois.length >= 1) {
					let range = this.showPois.length
					this.prizeIndex = Math.floor(Math.random() * range);

					let index = 0
					let sit = setInterval(() => {
						if (index >= this.showPois.length) {
							index = 0
						}
						this.showResult = this.showPois[index]["name"]
						console.log(this.showResult)
						index += 1
					}, 100)
					setTimeout(() => {
						clearInterval(sit)
						this.showResult = this.showPois[this.prizeIndex]["name"]
						uni.showToast({
							title: '已经筛选成功',
							icon: 'success', //将值设置为 success 或者 ''
							duration: 2000 //持续时间为 2秒
						})
					}, 1000)
				} else {
					console.log('筛选失败')
					uni.showModal({
						title: '警告信息',
						content: '您还没有选择主题，请您选择主题后重试',
						showCancel: false,
						success: function(res) {
							if (res.confirm) {
								console.log('用户点击确定');
							} else if (res.cancel) {
								console.log('用户点击取消');
							}
						}
					});
				}

			}
		}
	}
</script>

<style>
	.content {
		height: 100vh;
		width: 100vw;
		background-image: url('../../static/OIP.jpg');
	}

	.button {
		width: 70%;
		margin: 0 auto;
		display: flex;
		flex-direction: row;
	}

	.abc {
		width: 23%;
		height: 95upx;
		text-align: center;
		line-height: 100upx;
		background: red;
		border-radius: 50%;
		margin: 0 20upx;
	}

	.drink {
		width: 23%;
		height: 95upx;
		text-align: center;
		line-height: 100upx;
		background: #007AFF;
		border-radius: 50%;
		margin: 0 20upx;
	}

	.content-data {
		width: 85%;
		height: 550upx;
		border: 1px solid red;
		margin: 40upx auto;
	}

	.vIf {
		line-height: 550upx;
		text-align: center;
		color: red;
		height: 0upx;
	}

	.list {
		width: 80%;
		height: 500upx;
		margin: auto;
	}

	.select-list {
		overflow: auto;
	}

	.select-button {
		float: left;
		width: 25%;
		text-align: center;
		margin-top: 10upx;
		color: red;
	}

	.item {
		height: 45upx;
		line-height: 50upx;
		margin: 2upx 30upx;
		border: 1upx solid #d2000f;
		border-radius: 30upx;
		font-size: 25upx;
		text-align: center;
		display: flex;
		flex-direction: row;
		justify-content: center;
		align-items: center;
		color: #d2000f;
		background-color: #abd8cf;
		opacity: 0.8;
	}

	.screen {
		width: 20%;
		margin: 0 auto;
		animation-name: example;
		animation-duration: 4s;
	}

	.but {
		width: 85%;
		margin: 0 auto;
		display: flex;
		flex-direction: row;
	}

	.sel-but-drink {
		color: #0000FF;
	}

	.but text {
		color: rgb(0, 170, 127);
		line-height: 74upx;
	}

	@keyframes example {
		0% {
			background-color: red;
		}

		25% {
			background-color: yellow;
		}

		50% {
			background-color: blue;
		}

		100% {
			background-color: green;
		}
	}

	.display {
		text-align: center;
		margin-top: 100upx;
		color: #007AFF;
	}

	.none {
		display: none;
	}
</style>
