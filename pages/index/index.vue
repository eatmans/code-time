<template>
	<view class="c-container">
		<view class="c-title">
			<input class="c-input" type="text" :value="notie" placeholder="#添加计时内容" placeholder-class="c-input-pla" />
		</view>
		<view class="c-countdown" v-if="start">
			<u-count-down ref="uCountDown" :timestamp="startSec" :autoplay="autoplay" :show-days="false"
				@change="timeChange" :font-size="100" :separatorSize="65" :pause="pause"></u-count-down>
		</view>
		<view class="u-picker-body" v-if="!start">
			<picker-view :value="valueArr" @change="change" class="u-picker-view" @pickstart="pickstart"
				@pickend="pickend">
				<picker-view-column>
					<view class="u-column-item" v-for="(item, index) in hours" :key="index">
						{{ formatNumber(item) }}
						<text class="u-text" v-if="showTimeTag">时</text>
					</view>
				</picker-view-column>
				<picker-view-column>
					<view class="u-column-item" v-for="(item, index) in minutes" :key="index">
						{{ formatNumber(item) }}
						<text class="u-text" v-if="showTimeTag">分</text>
					</view>
				</picker-view-column>
				<picker-view-column>
					<view class="u-column-item" v-for="(item, index) in seconds" :key="index">
						{{ formatNumber(item) }}
						<text class="u-text" v-if="showTimeTag">秒</text>
					</view>
				</picker-view-column>
			</picker-view>
		</view>

		<view class="c-controller">
			<view class="c-default-list" v-if="!start">
				<view v-for="(time,index) in defaultTime" @tap="selectDefaultTime(time,index)" :key="time">
					<view class="c-time-item-sel" v-if="index == selectIndex">
						<view class="c-time-num">
							{{time}}
						</view>
						<view class="c-time-text-sel">
							分钟
						</view>
					</view>
					<view class="c-time-item" v-if="index != selectIndex">
						<view class="c-time-num">
							{{time}}
						</view>
						<view class="c-time-text">
							分钟
						</view>
					</view>

				</view>

			</view>
<!-- 按钮 -->
			<view class="c-controller-area" v-if="start">
				<view :animation="animationToLeftData" class="c-button" @tap="restartCountTime()">
					<image class="c-icon" src="../../static/restart.png"></image>
				</view>
				<view :animation="animationToRightData" class="c-button" @tap="stopCountTime()" v-if="!pause">
					<image class="c-icon" src="../../static/stop.png"></image>
				</view>
				<view class="c-button" @tap="stopCountTime()" v-if="pause">
					<image class="c-icon" src="../../static/start.png"></image>
				</view>
			</view>
			<view class="c-controller-area" v-else>
				<view class="c-button" @tap="startCountTime()">
					<image class="c-icon" src="../../static/start.png"></image>
				</view>
			</view>
		</view>
	</view>

</template>

<script>
	export default {
		data() {
			return {
				days: [],
				hours: [],
				minutes: [],
				seconds: [],
				start: false, // 是否开始倒计时
				stop: false, // 是否处于暂停状态
				pause: false, // 是否处于暂停状态
				day: 0,
				hour: 0,
				minute: 0,
				second: 0,
				reset: false,
				mode: "time",
				startDate: 0,
				endDate: '',
				startSec: 60, // 开始秒数
				currentSec: 0, // 当前剩余的秒数
				selectTime: 0, // 选择开始的秒数
				selectIndex: 0, // 选择的下标
				stopTime: 0, //暂停的时间
				autoplay: false,
				valueArr: [],
				timestamp: 300, // 默认五分钟
				showTimeTag: true,
				defaultTime: [5, 10, 15],
				notie: "",
				moving: false, // 列是否还在滑动中，微信小程序如果在滑动中就点确定，结果可能不准确
				// 动画
				animation:null,
				animationToLeftData:{},
				animationToRightData:{},
			};
		},
		mounted() {
			this.init();
		},
		computed: {

		},
		watch: {
			propsChange() {
				this.reset = true;
				setTimeout(() => this.init(), 10);
			},

			// 微信和QQ小程序由于一些奇怪的原因(故同时对所有平台均初始化一遍)，需要重新初始化才能显示正确的值
			value(n) {
				if (n) {
					this.reset = true;
					setTimeout(() => this.init(), 10);
				}
			}
		},
		methods: {
			// 标识滑动开始，只有微信小程序才有这样的事件
			pickstart() {
				// #ifdef MP-WEIXIN
				this.moving = true;
				// #endif
			},

			// 标识滑动结束
			pickend() {
				// #ifdef MP-WEIXIN
				this.moving = false;
				// #endif
			},

			// 开始倒计时
			startCountTime() {
				this.start = true;
				this.startSec = this.timestamp;
				this.autoplay = true;
				this.pause = false;
				console.log("开始倒计时：", this.startDate);
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
				this.animation = leftButton
				 leftButton.translateX(-60).step()
				this.animationToLeftData = leftButton.export()
				this.animation = leftButton
				 rightButton.translateX(60).step()
				this.animationToRightData = rightButton.export()
			},
			
			restartCountTime() {
				this.start = false;
				this.startDate = this.timestamp;
				console.log("点击重置的时间：", this.currentSec)
				this.pause = !this.pause;
			},

			stopCountTime() {
				// console.log('stop');
				this.pause = !this.pause;
			},
			// 对单列和多列形式的判断是否有传入变量的情况
			getItemValue(item, mode) {
				// 目前(2020-05-25)uni-app对微信小程序编译有错误，导致v-if为false中的内容也执行，错误导致
				// 单列模式或者多列模式中的getItemValue同时被执行，故在这里再加一层判断
				if (this.mode == mode) {
					return typeof item == 'object' ? item[this.rangeKey] : item;
				}
			},
			// 小于10前面补0，用于月份，日期，时分秒等
			formatNumber(num) {
				return +num < 10 ? '0' + num : String(num);
			},
			// 生成递进的数组
			generateArray: function(start, end) {
				// 转为数值格式，否则用户给end-year等传递字符串值时，下面的end+1会导致字符串拼接，而不是相加
				start = Number(start);
				end = Number(end);
				end = end > start ? end : start;
				// 生成数组，获取其中的索引，并剪出来
				return [...Array(end + 1).keys()].slice(start);
			},
			getIndex: function(arr, val) {
				let index = arr.indexOf(val);
				// 如果index为-1(即找不到index值)，~(-1)=-(-1)-1=0，导致条件不成立
				return ~index ? index : 0;
			},
			//日期时间处理
			initTimeValue() {
				let time = new Date();
				// 获取时分秒
				this.hour = time.getHours();
				this.minute = time.getMinutes();
				this.second = time.getSeconds();
			},
			init() {
				this.valueArr = [];
				this.reset = false;

				this.initTimeValue();

				// if (this.hour) {
				this.valueArr.push(0);
				this.setHours();
				// }
				// if (this.minute) {
				this.valueArr.push(0);
				this.setMinutes();
				// }
				// if (this.second) {
				this.valueArr.push(0);
				this.setSeconds();
				// }
				this.minute = 5

			},

			selectDefaultTime(time, index) {

				this.timestamp = time * 60;
				this.second = 0;
				this.minute = time;
				// console.log(this.minute)
				this.selectIndex = index;
				this.hour = 0;
				this.valueArr[0] = 0;
				this.valueArr[1] = time;
				this.valueArr[2] = 0;
				this.setHours()
				this.setMinutes();
				this.setSeconds();

			},

			// 事件触发，每秒一次
			timeChange(timestamp) {
				this.currentSec = timestamp;
			},

			setHours() {
				this.hours = this.generateArray(0, 23);
				this.valueArr.splice(this.valueArr.length - 1, 1, this.getIndex(this.hours, this.hour));
			},
			setMinutes() {
				this.minutes = this.generateArray(0, 59);
				this.valueArr.splice(this.valueArr.length - 1, 1, this.getIndex(this.minutes, this.minute));
			},
			setSeconds() {
				this.seconds = this.generateArray(0, 59);
				this.valueArr.splice(this.valueArr.length - 1, 1, this.getIndex(this.seconds, this.second));
			},

			close() {
				this.$emit('input', false);
			},

			// 用户更改picker的列选项
			change(e) {
				this.valueArr = e.detail.value;
				let i = 0;
				console.log("this.valueArr", this.valueArr)
				this.hour = this.hours[this.valueArr[i++]];
				this.minute = this.minutes[this.valueArr[i++]];
				this.second = this.seconds[this.valueArr[i++]];
				this.getTimeToSecond(this.hour, this.minute, this.second);
				// console.log(this.hour, "时", this.minute, "分", this.second, "秒")
			},

			// 将picker的时间专成秒数
			getTimeToSecond(hour, minute, second) {
				var time = 0;
				if (hour) {
					time += hour * 60 * 60;
				}
				if (minute) {
					time += minute * 60;
				}
				if (second) {
					time += second;
				}
				// console.log("总秒数", time)
				this.timestamp = time;
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

	.c-title {
		padding: 20rpx;
		margin: 20rpx;
		color: #007aff;
		font-size: 45rpx;
		width: 90%;
		// background-color: #C0C0C0;
	}

	.c-input {
		width: 100%;
		text-align: center;
		font-size: 45rpx;
		// background-color: #3F536E;
	}

	.c-input-pla {
		color: #e7e7e7;
	}

	.c-countdown {
		margin-top: 50rpx;
	}

	.u-picker-view {
		height: 100%;
		width: 700rpx;
		box-sizing: border-box;
		margin: 0 auto;
	}

	.u-picker-body {
		margin:50rpx 0 0 0;
		width: 100%;
		height: 500rpx;
		overflow: hidden;
		background-color: #ffffff;
	}

	.u-column-item {
		display: flex;
		flex-direction: row;
		align-items: center;
		justify-content: center;
		font-size: 50rpx;
		color: #000000;
		padding: 0 8rpx;
	}

	.u-text {
		font-size: 24rpx;
		padding-left: 8rpx;
	}

	.c-default-list {
		display: flex;
		flex-direction: row;
		justify-content: center;
		align-items: center;
	}

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
		// box-shadow: 0 4px 10px 0 rgba(0, 0, 0, 0.1), 0 3px 10px 0 rgba(0, 0, 0, 0.15);
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

	.c-controller-area {
		display: flex;
		justify-content: center;
		align-items: center;
	}

	.c-button {
		position: absolute;
		display: flex;
		flex-direction: column;
		justify-content: center;
		align-items: center;
		width: 125rpx;
		height: 125rpx;
		border-radius: 50%;
		padding: 20rpx;
		margin: 20rpx;
		background-color: #ffffff;
		box-shadow: 0 4px 10px 0 rgba(0, 0, 0, 0.1), 0 3px 10px 0 rgba(0, 0, 0, 0.15);
	}


	.c-time-num {
		font-size: 45rpx;
	}

	.c-time-text {
		font-size: 20rpx;
		color: #808080;
	}
	
	.c-time-text-sel{
		font-size: 20rpx;
		color: #d0f0f7;
	}

	.c-icon {
		width: 50rpx;
		height: 50rpx;
	}

	.c-controller {
		position: absolute;
		display: flex;
		flex-direction: column;
		justify-content: center;
		align-items: center;
		width: 100%;
		bottom: 120rpx;
	}
</style>
