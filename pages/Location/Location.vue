<template>
	<view class="Location">
		<image src="../../static/diz.png" class="img"></image>
		<text class="text">{{ addressName }}</text>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				addressName: '详细地理位置'
			}
		},
		props: ['addressName'],
		methods: {

		}
	}
</script>

<style>
	.Location {
		width: 70%;
		margin: 0 auto;
		padding: 100upx 0;
	}

	.img {
		width: 10%;
		height: 40upx;
	}

	.text {
		margin-left: 20upx;
	}
</style>
