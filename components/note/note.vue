<template>
	<view class="c-title">
		<input class="c-input" type="text" :value="note" placeholder="#添加计时内容" placeholder-class="c-input-pla" />
	</view>
</template>

<script>
	export default {
		name:"note",
		props:{
			note:{
				type:String,
				default:''
			}
		}
	}
</script>

<style lang="scss">
.c-title {
		padding: 50rpx 20rpx 20rpx 20rpx;
		margin: 20rpx;
		color: #007aff;
		font-size: 45rpx;
		width: 90%;
		.c-input {
			width: 100%;
			text-align: center;
			font-size: 45rpx;
			// background-color: #3F536E;
		}
		
		.c-input-pla {
			color: #e7e7e7;
		}
	}
</style>
