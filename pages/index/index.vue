<template>
	<view class="container">
		<button @click="uploadImage">选取图片识别</button>
		<view class="vvv">
			{{contents.words}}
			<!-- <text v-for="(item,index) in contents" :key="index">
				<test>{{item}}</test>
			</text> -->
			<!-- <text v-for="(item,index) in content" :key="index">
				<test>{{item.words}}</test>
			</text> -->
		</view>
	</view>
</template>
<script>
	import {
		pathToBase64
	} from '../../js_sdk/mmmm-image-tools/index.js'
	export default {
		data() {
			return {
				src: '',
				content: [],
				contents:'',
				imgUrl: ''
			}
		},
		methods: {
			async uploadImage() { //   选取照片，进行OCR识别
				const that = this
				this.contents=''
				 uni.chooseImage({ // 调用手机摄像头
					count: 1, //默认9
					sizeType: ['original','compressed'], //可以指定是原图还是压缩图，默认二者都有
					sourceType: ['camera '], //打开摄像或者相册
					success: function(res) {
						pathToBase64(res.tempFilePaths[0]).then(res => {
							that.imgUrl = res
						})
						// this.imgUrl = that.compressImg(res.tempFilePaths[0])
						// uni.previewImage({
						// 	// 对选中的图片进行预览
						// 	urls: res.tempFilePaths,
						// 	// urls:['','']  图片的地址 是数组形式

						// })
						uni.request({ // 获取token
							url: 'https://aip.baidubce.com/oauth/2.0/token',
							method: 'POST',
							data: {
								"client_id": "zXaVpDMPKyUvSLHC4WRd0M0G",
								"client_secret": "uC3ePqPv8D108UFUT0G6YNUCyzguOrsQ",
								"grant_type": "client_credentials",
							},
							header: {
								'content-type': 'application/x-www-form-urlencoded'
							},
							success: (res) => {
								console.log('222222222')
								 uni.request({ // 请求百度API
									url: `https://aip.baidubce.com/rest/2.0/ocr/v1/general?access_token=${res.data.access_token}`,
									data: {
										image: that.imgUrl,
										language_type: 'CHN_ENG', //识别语言类型，默认中英文结合
										detect_direction: true, //检测图像朝向
									},
									method: 'POST',
									header: {
										'Content-Type': 'application/x-www-form-urlencoded'
									},
									success: (res) => {
										uni.showLoading({
										    title: '加载中'
										});
										that.$nextTick(function(){
											console.log(res)
											// that.content = res.data.words_result
											that.contents = res.data.words_result[43]
										})
										uni.hideLoading();
										// this.content = res.data.words_result 
										console.log(that.content, '666666666666')
										// this.content=this.content.splice(0,0)
										// console.log(this.content)
									}
								})
							}
						})
					}
				})
			},

		}
	}
</script>

<style>
	.vvv {
		width: 99%;
		border: 2px dashed #eeeeee;
		height: 700px;
		margin: 30px 0 0 0;
	}

	.container {
		margin-top: 40px;
	}
</style>
