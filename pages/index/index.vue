<template>
	<view id="container">
		<view id="two_btn" class="btn-group">
			<button @click="uploadPic">上传图片</button>
			<button @click="downLoadPic">保存头像</button>
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
				url: 'https://pan.jialidun.vip/head/',
				top_pic: '',
				back_pic: '',
				img_index: 0,
				images_count: 26,
				images: [],
				hide_preview: false,
				bg_image:''
			}
		},
		onLoad() {
			uni.showLoading({
				title: '加载中...',
				mask: true
			})
			
	
			for (let i = 1; i <= this.images_count; i++) {
				uni.getImageInfo({
					src: this.url + i + '.png',
					success: (res) => {
						this.images.push(res.path)
						if (this.top_pic == "") {
							this.top_pic = res.path
						}
						if(this.images.length == 3)	{
							uni.hideLoading()
						}
					}
				})
			}
		},
		methods: {
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
		background: url("https://pan.jianwi.cn/wx%E6%AF%95%E4%B8%9A%E5%AD%A3bg.jpg");
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
		color: white;
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
		width: 225rpx;
		background-color: transparent;
		font-weight: 700;
		border: 1px solid #FFFFFF;
		border-radius: 50rpx;
		color: #FFFFFF;
		font-size: 30rpx;
		margin-left: 50rpx;
		margin-right: 50rpx;
		padding: 0px 25rpx;
	}
	.background-image{
	   height : 100%;
	    position: absolute;
	    width: 100%;
	    left: 0px;
	    top: 0px;
	}
</style>
