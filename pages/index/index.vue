<template>
	<view class="c-container">
		<note :note="note"></note>
		<view class="c-countdown" v-if="start">
			<u-count-down 
			ref="uCountDown" 
			:timestamp="timestamp" 
			:autoplay="autoplay" 
			:show-days="false"
			:font-size="100" 
			:separatorSize="65" 
			:pause="pause"
			></u-count-down>
		</view>
		<date-selector v-else ref="dateSelectorRef" showTimeTag @pickend="pickend"></date-selector>
		<controller-area 
		@startCountTime="startCountTime" 
		@stopCountTime="stopCountTime" 
		@clickSetTime="clickSetTime"
		:defaultTimeList="defaultTime" 
		:timestamp="timestamp"
		:pause="pause" 
		:start="start"
		class=""
		></controller-area>
	</view>

</template>

<script>
	export default {
		data() {
			return {
				start: false, // 是否开始倒计时
				pause: false, // 是否处于暂停状态
				autoplay: false,
				timestamp: 300, // 默认五分钟
				defaultTime: [5, 10, 15],
				note: "",
			};
		},
		methods: {
			// 开始倒计时
			startCountTime() {
				if (this.start) {
					// 正在计时，停止它
					console.log('restart');
					this.start = false;
					this.pause = false;
				} else {
					// 没有计时，开始计时
					console.log('start');
					this.start = true;
					this.autoplay = true;
					this.pause = false;
				}
			},
			pickend(timestamp){
				this.timestamp=timestamp
			},
			stopCountTime() {
				this.pause = !this.pause;
			},

			clickSetTime(minutes) {
				this.timestamp = minutes * 60;
				this.$refs.dateSelectorRef.quickSetMinutes(minutes)
			},

		}
	};
</script>

<style lang="scss" scoped>
	.c-container {
		display: flex;
		flex-direction: column;
		justify-content: center;
		align-items: center;
	}
	.c-countdown {
		margin-top: 50rpx;
	}

</style>
