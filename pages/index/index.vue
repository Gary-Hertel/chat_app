<template>
	<view class="container">
		<view class="whole-box">
			<image class="bg-set"
				src="https://tse3-mm.cn.bing.net/th/id/OIP-C.qc7V8aJ5HtFWLBZ4VmPMIwHaNK?w=187&h=333&c=7&r=0&o=5&dpr=2&pid=1.7">
			</image>
			<view><br /></view>
			<view><br /></view>
			<view><br /></view>
			<view><br /></view>
			<view class="content text-center">
				<view>
					<text class="text-white text-xxl">欢迎来到</text>
					<text class="text-white text-xxl">Secret Chat</text>
					<view><br /></view>
				</view>
				
				<image mode="aspectFit" src="../../static/logo.png""></image>

				<view>
					<text class="text-white text-lg">阅后即焚</text>
				</view>

				<view>
					<text class="text-white text-xl">对称加密传输</text>
				</view>

				<view>
					<text class="text-white text-xl">不保留任何聊天记录</text>
				</view>

				<view>
					<view><br /></view>
					<view><br /></view>
					<view>
						<text class="text-white text-xl">已为您创建随机的聊天室</text>
					</view>
					<view>
						<text class="text-white text-xl">分享当前网页链接给好友即可加入聊天</text>
					</view>
					<view>
						<text class="text-white text-xl">点击进入:</text>
					</view>
					<text class="text-red text-xl" @click="goto('chat', room_id)">
						{{room_id}}
					</text>
				</view>

			</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				room_id: ""
			}
		},
		onLoad() {
			this.room_id = Math.random().toString(36).slice(-8);
		},
		methods: {
			goto(url, room_id) {
				uni.navigateTo({
					url: url + "?room_id=" + room_id
				})
			}
		}
	}
</script>

<style>
	.bg-set {
		position: fixed;
		width: 100%;
		height: 100%;
		top: 0;
		left: 0;
		z-index: -1;
	}
</style>
