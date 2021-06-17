<template>
<view class="u-picker-body">
			<picker-view :value="valueArr" @change="change" class="u-picker-view" @pickstart="pickstart"
				@pickend="pickend">
				<picker-view-column>
					<view class="u-column-item" v-for="(item, index) in hours" :key="index">
						{{ formatNumber(item) }}
						<text class="c-text" v-if="showTimeTag">时</text>
					</view>
				</picker-view-column>
				<picker-view-column>
					<view class="u-column-item" v-for="(item, index) in minutes" :key="index">
						{{ formatNumber(item) }}
						<text class="c-text" v-if="showTimeTag">分</text>
					</view>
				</picker-view-column>
				<picker-view-column>
					<view class="u-column-item" v-for="(item, index) in seconds" :key="index">
						{{ formatNumber(item) }}
						<text class="c-text" v-if="showTimeTag">秒</text>
					</view>
				</picker-view-column>
			</picker-view>
		</view>
</template>

<script>
	export default {
		name:"date-selector",
		created(){
			this.initTimeList()
		},
		data() {
			return {
				moving: false, // 列是否还在滑动中，微信小程序如果在滑动中就点确定，结果可能不准确
				hours: [],
				minutes: [],
				seconds: [],
				valueArr: [],
			};
		},
		props:{
			showTimeTag:{
				type:Boolean,
				default:false
			}
		},
		methods:{
			// 初始化
			initTimeList(){
				// 生成 时，分，秒
				this.valueArr.push(0);
				this.setHours();
				this.valueArr.push(0);
				this.setMinutes();
				this.valueArr.push(0);
				this.setSeconds();
				
			},
			getIndex(arr, val) {
				let index = arr.indexOf(val);
				// 如果index为-1(即找不到index值)，~(-1)=-(-1)-1=0，导致条件不成立
				return ~index ? index : 0;
			},
			// 小于10前面补0，用于月份，日期，时分秒等
			formatNumber(num) {
				return +num < 10 ? '0' + num : String(num);
			},
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
			// 生成递进的数组
			generateArray(start, end) {
				// 转为数值格式，否则用户给end-year等传递字符串值时，下面的end+1会导致字符串拼接，而不是相加
				start = Number(start);
				end = Number(end);
				end = end > start ? end : start;
				// 生成数组，获取其中的索引，并剪出来
				return [...Array(end + 1).keys()].slice(start);
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
	}
</script>

<style lang="scss">
	.u-picker-body {
		margin: 0;
		width: 100%;
		height: 500rpx;
		overflow: hidden;
		background-color: #ffffff;
		.u-picker-view {
			height: 100%;
			width: 700rpx;
			box-sizing: border-box;
			margin: 0 auto;
			.u-column-item {
				display: flex;
				flex-direction: row;
				align-items: center;
				justify-content: center;
				font-size: 50rpx;
				color: #000000;
				padding: 0 8rpx;
				.c-text {
					font-size: 24rpx;
					padding-left: 8rpx;
				}
			}
		}
		
	}
</style>
