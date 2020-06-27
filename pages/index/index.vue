<template>
	<view id="container">
		<view id="two_btn" class="btn-group">
			<!-- <button @click="uploadPic">上传图片</button> -->
			<button v-if="!canIUse" open-type="getUserInfo" @click="bindGetUserInfo" type="default">授权使用微信头像</button>
			<button @click="downLoadPic" v-else>保存头像</button>
		</view>
		<view>
			<view id="left_btn" @click="prev_img">
				◁
			</view>
			<view id="right_btn" @click="next_img">
				▷
			</view>
		</view>

		<view id="img_container">
			<image :src="top_pic" mode="aspectFit" style="background-color: unset;z-index: 1000;"></image>
			<image :src="back_pic" mode="scaleToFill" style="z-index: 999;"></image>
		</view>
		<view id="cav_container">
			<canvas canvas-id="cav"></canvas>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				url: 'cloud://xiankejidaxue-f8ra1.7869-xiankejidaxue-f8ra1-1302515215/head/',
				top_pic: '',
				back_pic: '',
				img_index: 0,
				images_count: 28,
				images: [],
				hide_preview: false,
				bg_image:'',
				canIUse: false
			}
		},
		onLoad() {
			uni.showLoading({
				title: '加载中...',
				mask: true
			})
			this.getUserInfo()
			wx.cloud.init()
			wx.cloud.downloadFile({
			  fileID: this.url+"1 (1).png", // 文件 ID
			  success: res => {
						this.top_pic = res.tempFilePath
		
			  },
			  fail: (e)=>{
				  console.log(e)
				  console.log("失败了"+i)
			  }
			})
			
			for (let i = 1; i <= this.images_count; i++) {
				// cloud://yibanyunwxavatar-e927e.7969-yibanyunwxavatar-e927e-1302400054/head/
				wx.cloud.downloadFile({
				  fileID: this.url+1+' ('+i+')'+".png", // 文件 ID
				  success: res => {
					{
						this.images.push(res.tempFilePath)
						if (this.top_pic == "") {
							console.log("当前是"+i)
							
							this.top_pic = this.images[0]
						}
						if(this.images.length == 3)	{
							if(this.canIUse){
								if(this.back_pic != ""){
										uni.hideLoading()
								}
							}else{
								uni.hideLoading()
							}
						}
					}
				  },
				  fail: (e)=>{
					  console.log(e)
					  console.log("失败了"+i)
				  }
				})
			}
		},
		methods: {
		 headimgHD(imageUrl) {
			        console.log('原来的头像', imageUrl);
			        
			        imageUrl = imageUrl.split('/');        //把头像的路径切成数组
			        
			        //把大小数值为 46 || 64 || 96 || 132 的转换为0
			        if (imageUrl[imageUrl.length - 1] && (imageUrl[imageUrl.length - 1] == 46 || imageUrl[imageUrl.length - 1] == 64 || imageUrl[imageUrl.length - 1] == 96 || imageUrl[imageUrl.length - 1] == 132)) {
			            imageUrl[imageUrl.length - 1] = 0;
			        }
			       
			        imageUrl = imageUrl.join('/');   //重新拼接为字符串
			 
			        console.log('高清的头像', imageUrl);
			 
			        return imageUrl;
			   },
			bindGetUserInfo(e){
				console.log(e)
				this.getUserInfo()
				console.log(e.detail.userInfo)
			},
			getUserInfo()
			{
				uni.getSetting({
					success:(res)=>{
						 if (res.authSetting['scope.userInfo']) {
						          // 已经授权，可以直接调用 getUserInfo 获取头像昵称
								  uni.showLoading({
								  	title: '头像正在加载中',
								  	mask: true
								  })
						          uni.getUserInfo({
						            success: (res) =>{
						              console.log(res.userInfo)
									  
									  uni.getImageInfo({
									  	src: this.headimgHD(res.userInfo.avatarUrl),
									  	success: (res) => {
											 this.back_pic = res.path
											 if(this.images.length > 2)	{
											 	uni.hideLoading()
											 }
									  	}
									  })
									 
									  this.canIUse = true
						            },
						          })
								  }else{
									  this.canIUse = false
									  if(this.images.length > 2)	{
									  	uni.hideLoading()
									  }
								  }
					}
				})
			},
			prev_img() {
				if (this.img_index == 0){
					this.img_index = this.images_count - 1
					return
				}
				this.top_pic = this.images[--this.img_index]
				console.log('lll')
			},
		
			next_img() {
				if (this.img_index == this.images_count - 1) {
					this.img_index = 1
					return
				}
				this.top_pic = this.images[++this.img_index]
				console.log('rrr')
			},
			downLoadPic() {
				let context = wx.createCanvasContext('cav')
				this.hide_preview = true
				context.drawImage(this.back_pic, 0, 0, 200, 200)
				context.drawImage(this.top_pic, 0, 0, 200, 200)
				context.draw(false, (res) => {
					uni.canvasToTempFilePath({
						canvasId: 'cav',
						quality: 1,
						destWidth: 800,
						destHeight: 800,
						success(res) {
							uni.saveImageToPhotosAlbum({
								filePath: res.tempFilePath
							})
							
						context.clearRect(0,0,200,200)
						context.draw(false)
						}
					})
				});
			},
			setTopPic(event) {
				this.top_pic = event.target.dataset.src
			},
			uploadPic() {
				uni.chooseImage({
					count: 1,
					sizeType: 'original',
					sourceType: ['album'],
					success: (res) => {
						this.back_pic = res.tempFilePaths[0]
					}
				})
			}

		}
	}
</script>

<style>
	#container {
		background: url("https://7869-xiankejidaxue-f8ra1-1302515215.tcb.qcloud.la/bg.jpg?sign=7d0fbe37a676cb87894dba984487368a&t=1593251969");
		background-size: cover;
		background-repeat: no-repeat;
		position: relative;
		width: 100%;
		height: 100vh;
	}

	#img_container {
		position: absolute;
		width: 350rpx;
		height: 350rpx;
		top: 650rpx;
		left: 198rpx;
	}

	#img_container image {
		position: absolute;
		margin: 0px;
		width: 100%;
		height: 100%;
		background-color: #F1F1F1;
	}

	#left_btn,
	#right_btn {
		position: absolute;
		top: 773rpx;
		font-size: 60rpx;
		color: black;
		background-color: transparent;
	}

	#left_btn {
		left: 60rpx;
	}

	#right_btn {
		right: 60rpx;
	}

	#two_btn {
		font-size: 1.5em;
		width: 750rpx;
		text-align: center;
		position: absolute;
		top: 1050rpx;
		color: black;
	}

	#cav_container {
		position: absolute;
		top: 650rpx;
		left: 198rpx;
		width: 350rpx;
		height: 350rpx;
	}
	canvas {
		width: 200px;
		height: 200px;
		margin: auto;
	}
	#two_btn button {
		display: inline-block;
		min-width: 225rpx;
		background-color: transparent;
		font-weight: 700;
		border: 1px solid black;
		border-radius: 50rpx;
		color: skygreen;
		font-size: 30rpx;
		margin-left: 50rpx;
		margin-right: 50rpx;
		padding: 0px 25rpx;
	}
	.background-image{
	    height: 100%;
	    position: absolute;
	    width: 100%;
	    left: 0px;
	    top: 0px;
	}
</style>
