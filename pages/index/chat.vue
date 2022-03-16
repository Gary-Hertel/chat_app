<template>
	
	<view class="container">
		<view class="whole-box">
			<image class="bg-set"
				src="https://tse4-mm.cn.bing.net/th/id/OIP-C.4dKL2YlpISczolgxXc4zMAHaN2?w=182&h=341&c=7&r=0&o=5&dpr=2&pid=1.7">
			</image>

			<view class="content">

				<view class="cu-chat" v-for="(item, index) in messages" :key="index">
					
					<view class="content text-center">
						<text class="text-gray">{{timeList[index]}}</text>
					</view>
					
					<view class="content  text-center">
						<text v-if="item.status=='join'" class="text-green">{{item.username}}加入了聊天</text>
						<text v-if="item.status=='left'" class="text-red">{{item.username}}离开了聊天</text>
					</view>

					<view class="cu-item" v-if="item.status=='speak' && item.username!=my_name">
						<view>
							<view class="content">
								<image class="cu-avatar radius"
									src="https://mrocket.cn/static/img/1641990783968_hdImg_9e6b02b56e56a24e78e618ee0d954c7f1639149871161.jpg">
								</image>
							</view>
							<text class="text-white text-left">{{item.username}}</text>
						</view>

						<view class="main">
							<view class="content shadow bg-white">
								<text>{{item.text}}</text>
							</view>
						</view>

					</view>
					
					<view class="cu-item self" v-if="item.status=='speak' && item.username==my_name">
					
						<view class="main">
							<view class="content shadow bg-green">
								<text>{{item.text}}</text>
							</view>
						</view>
						
						<view>
							<view class="content">
								<image class="cu-avatar radius"
									src="https://tva1.sinaimg.cn/large/e6c9d24ely1h0646pnzb9j208c08cdfz.jpg">
								</image>
							</view>
							<text class="text-green">{{item.username}}</text>
						</view>
					
					</view>

				</view>

				<view class="cu-bar foot input" :style="[{bottom:InputBottom+'px'}]">
					<input v-model="my_input" class="solid-bottom" :adjust-position="false" :focus="false"
						maxlength="300" cursor-spacing="10" @focus="InputFocus" @blur="InputBlur" @input="onInput"
						confirm-type="done" @confirm="sendMessage(my_name, my_input,'speak')"></input>
					<button class="cu-btn bg-green shadow" @touchend.prevent="sendMessage(my_name, my_input,'speak')">发送</button>
				</view>
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				InputBottom: 0,
				socketTask: null,
				is_open_socket: false,
				my_name: "", // 我的昵称
				messages: [], // 存放聊天记录的数组
				my_input: "", // 我的输入内容
				room_id: "" ,// 聊天室id
				timeList: [] // 时间数组
			};
		},
		mounted() {
			var a = document.getElementsByClassName('uni-page-head-hd')[0]
			a.style.display = 'none'
		},
		onLoad: function(option) {
			this.room_id = option.room_id;
			this.generate_name()
		},
		methods: {
			getLocalTime(){
				var d=new Date();
				var n=d.toLocaleString();
				return n;
			},
			sendJoinMessage() {
				var send_msg = JSON.stringify({
					"username": this.my_name,
					"text": this.username + " " + "join",
					"status": "join"
				})
				this.socketTask.send({
					data: send_msg,
					success(res) {
						console.log("消息发送成功", res)
					},
					fail(error) {
						console.error("消息发送失败", error);
					}
				});
			},
			generate_name() {
				uni.request({
					url: 'https://chat.mrocket.cn/api/random_name',
					data: {},
					header: {},
					success: (res) => {
						console.log(res.data.data);
						this.my_name = res.data.data;
						this.connect_socket();
						this.toBottom();
					}
				});
				// this.my_name = Math.random().toString(36).slice(-8)
			},
			onInput(e) {
				this.my_input = e.detail.value
			},
			InputFocus(e) {
				this.InputBottom = e.detail.height
			},
			InputBlur(e) {
				this.InputBottom = 0
			},
			connect_socket() {
				this.socketTask = uni.connectSocket({
					url: 'wss://chat.mrocket.cn/ws/' + this.room_id,
					success: (res) => {
						console.log(res)
					}
				});
				this.socketTask.onOpen((res) => {
					this.sendJoinMessage()
					this.toBottom()
					this.socketTask.onMessage((res) => {
						var data = JSON.parse(res.data);
						console.log(data);
						this.timeList.push(this.getLocalTime())
						this.messages.push(data)
						this.toBottom()
					});
				})
			},
			sendMessage(username, text, status) {
				if (!text) {
					uni.showToast({
						title: "无法发送空的消息",
						icon: 'none'
					})
					return
				}
				this.my_input = null
				var send_msg = JSON.stringify({
					"username": username,
					"text": text,
					"status": status
				})
				this.timeList.push(this.getLocalTime())
				this.messages.push({
					"username": username,
					"text": text,
					"status": status
				})
				this.toBottom()
				this.socketTask.send({
					data: send_msg,
					success(res) {
						console.log("消息发送成功", res)
					},
					fail(error) {
						console.error("消息发送失败", error);
					}
				});
			},
			// 关闭websocket【离开这个页面的时候执行关闭】
			closeSocket() {
				this.socketTask.close({
					success(res) {
						this.is_open_socket = false;
						console.log("关闭成功", res)
					},
					fail(err) {
						console.log("关闭失败", err)
					}
				})
			},
			toBottom() {
				uni.pageScrollTo({
					scrollTop: 999999999999,
					duration: 1,
					success(res) {
						console.log("滑到底部成功", res)
					},
					fail(error) {
						console.log("滑到底部失败", error)
					}
				})
			}
		}
	}
</script>

<style>
	page {
		padding-bottom: 100upx;
	}

	.bg-set {
		position: fixed;
		width: 100%;
		height: 100%;
		top: 0;
		left: 0;
		z-index: -1;
	}
</style>
