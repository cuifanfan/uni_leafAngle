<template>
	<view class="all">
		<view class="image">
			<view class="imageText" @click="seleckImage" v-if="imgSrc" style="vertical-align: top;">
				请选择要测量的图片
			</view>
			<view class="imgSrc" v-else>
				<image mode="heightFix" v-for="(item,index) in imgArr" :src="item" @click="preview(item)"
					@longtap="delImg"></image>
			</view>
		</view>
		<view class="btn">
			<button class="mini-btn" type="primary">开始测量</button>
			<button class="mini-btn" type="primary">保存结果</button>
		</view>
		<view class="show_img">
			<img class='set_img' src="../../../images/cuifan.jpg" alt="">
		</view>

		<view class="answer">
			<text>平台本次测试的叶夹角为：<span class="answer_num">35.647216°</span></text>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				imgArr: [], // 存放图片数组
				imgSrc: true // 控制文字和图片显隐
			}
		},
		methods: {
			seleckImage() {
				let that = this
				uni.chooseImage({
					count: 1, // 默认9
					sizeType: ['original', 'compressed'], // 可以指定是原图还是压缩图，默认二者都有
					sourceType: ['album', 'camera'], // 从相册选择
					success: function(res) {
						console.log('选择图片')
						console.log(res.tempFilePaths)
						// 将数组存放在数组中
						that.imgArr = res.tempFilePaths
						that.imgSrc = false
						uni.getImageInfo({
							src: res.tempFilePaths[0],
							success: function(img) {
								console.log('预览图片')
								console.log(img)
							},
							fail: function(err) {
								console.log(err)
							}
						})
					},
					fail: function(err) {
						console.log(err)
					}
				});
			},
			// 点击图片进行预览
			preview(img) {
				console.log('预览')
				// 新建一个存放预览图片的空数组
				var imgArr = []
				imgArr.push(img)
				uni.previewImage({
					urls: imgArr,
					current: imgArr[0],
					longPressActions: {
						itemList: ['发送给朋友', '保存图片', '收藏'],
						success: function(data) {
							console.log('选中了第' + (data.tapIndex + 1) + '个按钮,第' + (data.index + 1) + '张图片');
						},
						fail: function(err) {
							console.log(err.errMsg);
						}
					}
				});
			},
			// 长按删除图片
			delImg() {
				let that = this
				uni.showModal({
					title: '删除图片',
					content: '确定删除图片?',
					success: res => {
						if (res.confirm) {
							// 删除图片 将数据置空
							that.imgSrc = true
							that.imgArr = []
						} else if (res.cancel) {
							console.log('用户点击取消');
						}
					}
				})
			}
		}
	}
</script>

<style lang="less">
	.all {
		padding: 20rpx;
		font-size: 40rpx;

		.image {

			box-sizing: border-box;
			border: 2rpx dashed #666666;
			height: 440rpx;

			.imageText {
				text-align: center;
				line-height: 440rpx;
			}

			.imgSrc {
				height: 440rpx;
				text-align: center;

				image {
					height: 100%;
				}
			}
		}
	}

	.btn {
		margin-top: 30rpx;
		display: flex;
		justify-content: space-between;
	}


	.show_img {
		display: flex;
		justify-content: center;
		align-items: center;
		padding: 30rpx 0;
	}

	.set_img {
		width: 50%;
	}

	.answer {
		text-align: center;
	}

	.answer_num {
		color: red;
	}
</style>
