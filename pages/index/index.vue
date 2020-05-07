<template>
	<view>
		<view class="head" style="text-align: center;margin: 10px;">
			<text>毕业季头像生成</text>
		</view>
		<scroll-view class="scroll-x" show-scrollbar="true" scroll-left="1060" scroll-with-animation="true" scroll-x="true" >
			<view class="scroll-item">
				<image :data-src='img' :src="img" :key="index" @click="setTopPic" v-for="(img,index) in images" mode="aspectFit"></image>
			</view>
		</scroll-view>
		<view class="btn-group">
			<button @click="uploadPic" type="primary" size="mini">上传图片</button>
			<button @click="downLoadPic" type="primary" size="mini">保存头像</button>
		</view>
		
		<view class="preview_pic">
			<view>
				<text>预览</text>
			</view>
			<image ref='top_pic' :src="top_pic" style="z-index: 999;" mode="aspectFit"></image>
			<image :src="back_pic" mode="scaleToFill"></image>
		</view>
		
		<view style="margin-top: 100upx;">
			<view style="text-align: center;">
				<text>Result...</text>
			</view>
			 <canvas style="width: 200px; height: 200px;margin: auto;" canvas-id="cav"></canvas>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				url: 'https://pan.jialidun.vip/head/',
				title: 'Hello',
				top_pic: '',
				back_pic: '',
				images_count:26,
				images:[],
				hide_preview:false,
			}
		},
		onLoad() {
			uni.showLoading({
				mask:true,
				title:'正则加载头像资源，请等待片刻。'
			})
			for(let i = 1;i <= this.images_count;i++){
				uni.getImageInfo({
				  src: this.url+i+'.png',
				  success: (res)=>{
				    this.images.push(res.path)
				  }
				})
			}
			uni.hideLoading()
		},
		methods: {
			downLoadPic()
			{
				let context = wx.createCanvasContext('cav')
				this.hide_preview = true
				context.drawImage(this.back_pic,0,0,200,200)
				context.drawImage(this.top_pic,0,0,200,200)
				context.draw(true,(res)=>{
					uni.canvasToTempFilePath({
						canvasId: 'cav',
						quality:1,
						destWidth:800,
						destHeight:800,
						success(res) {
							uni.saveImageToPhotosAlbum({
								filePath:res.tempFilePath
							})
						}
					})
				});
			},
			setTopPic(event)
			{
				this.top_pic = event.target.dataset.src
			},
			uploadPic()
			{
				uni.chooseImage({
					count:1,
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
	.preview_pic{
		text-align: center;
		height: 370rpx;
	}
	.preview_pic image{
		margin: 0px;
		padding: 0px;
		width: 350rpx;
		height: 350rpx;
		position: absolute;
		left: 200rpx;
		background: none;
		border: #F1F1F1 1px solid;
	}
	.btn-group{
		text-align: center;
	}
	button{
		margin: 10rpx;
	}
	image{
		width: 230rpx;
		height: 230rpx;
		margin: 13rpx;
		padding: 3rpx;
		background: #F1F1F1;
	}
	.scroll-x{
		width: 580rpx;
		overflow: hidden;
		white-space: nowrap;
		height: 300upx;
		margin-left: 85rpx;
		margin-right: 85rpx;
	}

	.scroll-item{
		width: 300px;
		display: inline-block;
		height: 300upx;
	}
</style>
