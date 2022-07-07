<template>
	<view>
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
		<view>
			<view :class="['list', inv == 0 ? 'play' : 'take']">
				0
			</view>
			<view :class="['list', inv == 1 ? 'play' : 'take']">
				1
			</view>
			<view :class="['list', inv == 2 ? 'play' : 'take']">
				2
			</view>
			<view :class="['list', inv == 3 ? 'play' : 'take']">
				3
			</view>
		</view>
	</view>
</template>

<script>
	import amap from '../../js_Sdk/amap-wx.js'
	export default {
		data() {
			return {
				
			}
		},
		
	}
</script>

<style>
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

	.list {
		width: 85%;
		height: 500upx;
		border: 1px solid red;
		margin: 40upx auto;
	}

	.take {
		display: none;
	}

	.play {
		display: block;
	}
</style>
