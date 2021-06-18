<template>
	<view class="c-controller">
		<!-- 快捷设置按钮 -->
		<view :animation="animationQuickChooseTime" class="c-default-list">
			<view v-for="(time,index) in defaultTimeList" v-if="defaultTimeList.length!==0"
				@tap="selectDefaultTime(time,index)" :key="time">
				<view :class="[index == selectIndex&&time*60==timestamp?'c-time-item-sel':'c-time-item']">
					<view class="c-time-num">
						{{time}}
					</view>
					<view :class="[index == selectIndex&&time*60==timestamp?'c-time-text-sel':'c-time-text']">
						分钟
					</view>
				</view>
			</view>

		</view>
		<!-- 控制按钮 -->
		<view class="c-controller-area">
			<view :animation="animationToLeftData" class="c-button" style="z-index: 1;" @tap="startCountTime()">
				<image v-if="start" class="c-icon" src="../../static/restart.png"></image>
				<image v-else class="c-icon" src="../../static/start.png"></image>
			</view>
			<view :animation="animationToRightData" class="c-button" @tap="stopCountTime()">
				<image v-if="!pause" class="c-icon" src="../../static/stop.png"></image>
				<image v-else class="c-icon" src="../../static/start.png"></image>
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		name: "controller-area",
		data() {
			return {
				// 动画
				animationToLeftData: {},
				animationToRightData: {},
				animationQuickChooseTime: {},
				selectIndex: null
			};
		},
		methods: {
			startCountTime() {
				if (this.start) {
					// 正在计时，停止它
					this.$emit('startCountTime', this.start)
					// 执行动画
					let leftButton = uni.createAnimation({
						transformOrigin: "50% 50%",
						duration: 500,
						timingFunction: "ease",
						delay: 0
					})
					// 执行动画
					let rightButton = uni.createAnimation({
						transformOrigin: "50% 50%",
						duration: 500,
						timingFunction: "ease",
						delay: 0
					})
					// 执行动画
					let quickChooseTimeButton = uni.createAnimation({
						transformOrigin: "50% 50%",
						duration: 300,
						timingFunction: "ease",
						delay: 0
					})
					leftButton.translateX(0).step()
					this.animationToLeftData = leftButton.export()
					rightButton.translateX(0).opacity(0).step()
					this.animationToRightData = rightButton.export()
					quickChooseTimeButton.scale(1.1).step()
					quickChooseTimeButton.scale(1).step()
					this.animationQuickChooseTime = quickChooseTimeButton.export()
				} else {
					// 没有计时，开始计时
					this.$emit('startCountTime', this.start)
					// 执行动画
					let leftButton = uni.createAnimation({
						transformOrigin: "50% 50%",
						duration: 500,
						timingFunction: "ease",
						delay: 0
					})
					// 执行动画
					let rightButton = uni.createAnimation({
						transformOrigin: "50% 50%",
						duration: 500,
						timingFunction: "ease",
						delay: 0
					})
					// 执行动画
					let quickChooseTimeButton = uni.createAnimation({
						transformOrigin: "50% 50%",
						duration: 500,
						timingFunction: "ease",
						delay: 0
					})
					leftButton.translateX(-60).step()
					this.animationToLeftData = leftButton.export()
					rightButton.opacity(1).translateX(60).step()
					this.animationToRightData = rightButton.export()
					quickChooseTimeButton.scale(0).step()
					this.animationQuickChooseTime = quickChooseTimeButton.export()
				}

			},
			stopCountTime() {
				this.$emit('stopCountTime')
			},
			selectDefaultTime(time, index) {
				this.$emit('clickSetTime', time)
				this.selectIndex = index;

			},
		},
		props: {
			timestamp: {
				type: Number,
				default: 0
			},
			defaultTimeList: {
				type: Array,
				default: []
			},
			pause: {
				type: Boolean,
				default: false
			},
			start: {
				type: Boolean,
				default: false
			}
		}
	}
</script>

<style lang="scss">
	.c-controller {
		position: absolute;
		top: 750rpx;
		left: 0;
		height: 400rpx;
		width: 100%;

		.c-default-list {
			display: flex;
			justify-content: center;
			position: absolute;
			 top:0;
			   left: 0;
			   right: 0;
			   bottom: 0;
			   margin:auto;
			

			.c-time-item {
				display: flex;
				flex-direction: column;
				justify-content: center;
				align-items: center;
				width: 125rpx;
				height: 125rpx;
				border-radius: 50%;
				padding: 20rpx;
				margin: 20rpx;
				background-color: #eeeeee;

			}


			.c-time-item-sel {
				display: flex;
				flex-direction: column;
				justify-content: center;
				align-items: center;
				width: 125rpx;
				height: 125rpx;
				border-radius: 50%;
				padding: 20rpx;
				margin: 20rpx;
				color: #FFFFFF;
				background-color: #007aff;

			}

			.c-time-item,
			.c-time-item-sel {
				.c-time-num {
					font-size: 45rpx;
				}

				.c-time-text {
					font-size: 20rpx;
					color: #808080;
				}

				.c-time-text-sel {
					font-size: 20rpx;
					color: #d0f0f7;
				}
			}


		}


		.c-controller-area {
			height: 200rpx;
			width: 100%;
			position: absolute;
			top: 200rpx;
			left: 0;
			margin: 0;

			.c-button {
				position: absolute;
				display: flex;
				flex-direction: column;
				justify-content: center;
				align-items: center;
				width: 125rpx;
				height: 125rpx;
				border-radius: 50%;
				background-color: #ffffff;
				box-shadow: 0 4px 10px 0 rgba(0, 0, 0, 0.1), 0 3px 10px 0 rgba(0, 0, 0, 0.15);
				top:0;
				  left: 0;
				  right: 0;
				  bottom: 0;
				  margin:auto;

				/* 宽度的一半 */
				.c-icon {
					width: 50rpx;
					height: 50rpx;
				}

			}
		}
	}
</style>
