<template>
	<view class="vie">
		<image src="../../static/index/appshou.png" mode="aspectFill"></image>
		<!-- <view class="time_n" @click="go_ind(1)">
			{{arr}}s 立即跳过
		</view> -->
	</view>
</template>

<script>
	export default{
		data(){
			return{
				arr:0,
				page_img:'',
				log_type:0
			}
		},
		onShow() {
			
			this.num()
		},
		methods:{
			num(){
				this.arr = 3 
				let tim = setInterval(()=>{
					this.arr -= 1
					if(this.arr == 0){
						if(this.log_type == 0){
							clearInterval(tim)
							this.go_ind(this.log_type)
						}else{
							clearInterval(tim)
						}
					}
				},1000)
				return
				this.api.get('images').then(res=>{
					console.log(res)
					if(res.status == 1){
						this.page_img = res.data.image
						
						
					}
				})
			},
			go_ind(e){
				this.log_type = e
				if(!uni.getStorageSync('token')){
					uni.redirectTo({
						url:'./login',
						animationType: 'fade-in'
					})
				}else{
					uni.switchTab({
						url:'../index/index',
						animationType: 'fade-in'
					})
				}
			}
		}
	}
</script>


<style lang="scss" scoped>
	.vie{
		width: 100%;
		height: 100vh;
		background-size: 100% 100vh;
		image{
			width: 100%;height: 100vh;
		}
		.time_n{
			height: 70rpx;line-height: 70rpx;text-align: center;color: white;padding: 0 20rpx;
			background-color: rgba(0,0,0,0.4);border-radius: 50rpx;
			position: fixed;right: 30rpx;top: 160rpx;
		}
	}
</style>
