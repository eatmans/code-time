<template>
	<view class="c-container">
		<note :note="note"></note>
		<view class="c-countdown" v-if="start">
			<u-count-down 
			ref="uCountDown" 
			:timestamp="startSec" 
			:autoplay="autoplay" 
			:show-days="false"
			:font-size="100" 
			:separatorSize="65" 
			:pause="pause"
			></u-count-down>
		</view>
		<date-selector :showTimeTag="showTimeTag"></date-selector>
		<controller-area @startCountTime="startCountTime" @stopCountTime="stopCountTime" @clickSetTime="clickSetTime"
			:defaultTimeList="defaultTime" :pause="pause" :start="start"></controller-area>
	</view>

</template>

<script>
	export default {
		data() {
			return {
				start: false, // 是否开始倒计时
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
				stopTime: 0, //暂停的时间
				autoplay: false,
				valueArr: [],
				timestamp: 300, // 默认五分钟
				showTimeTag: true,
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
					this.startDate = this.timestamp;
					console.log("点击重置的时间：", this.currentSec)
					this.pause = false;
				} else {
					// 没有计时，开始计时
					console.log('start');
					this.start = true;
					this.startSec = this.timestamp;
					this.autoplay = true;
					this.pause = false;
				}

			},

			stopCountTime() {
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

			// 生成递进的数组
			generateArray(start, end) {
				// 转为数值格式，否则用户给end-year等传递字符串值时，下面的end+1会导致字符串拼接，而不是相加
				start = Number(start);
				end = Number(end);
				end = end > start ? end : start;
				// 生成数组，获取其中的索引，并剪出来
				return [...Array(end + 1).keys()].slice(start);
			},
			getIndex(arr, val) {
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

				// 生成 时，分，秒
				this.valueArr.push(0);
				this.setHours();
				this.valueArr.push(0);
				this.setMinutes();
				this.valueArr.push(0);
				this.setSeconds();
				this.minute = 5

			},
			clickSetTime(time) {
				this.timestamp = time * 60;
				this.second = 0;
				this.minute = time;
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

			close() {
				this.$emit('input', false);
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
