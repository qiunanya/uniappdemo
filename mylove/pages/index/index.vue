<template>
	<view class="content">
		<!-- <image class="logo" src="/static/logo.png"></image> -->
		<view class="text-area">
		    <!--<text class="title">{{title}}</text> -->
			<!-- <V5Button name="v5" host="192.168.254.108" token="bbed7c13-91d8-4c16-a172-9d1d62b0b6f0"/> --> <!-- name代表了组件的表单域名称，必填 -->
			<!-- <move-verify @result='verifyResult' ref="verifyElement"></move-verify> -->
			<carousel :img-list="imgList" url-key="url" @selected="selectedBanner"/>
			<!-- <com-upload-file ref="comUploadFiles" @sendFiles="getUploadFile" @removeFile="deleteFiles"></com-upload-file> -->
		    <!-- <mark-slide-list :list="list" :button="buttonList" :border="true" @click="clickMethod" @change="changeMethod"></mark-slide-list> -->
		</view>
		<!-- <img-upload class="content" async :name="12345" :imgArr="imgs" imgCount="9" ref="imgUpload" :url="imgUploadUrl" @result="resultUrl($event)" @delImg="($event)" ></img-upload> -->
	</view>
</template>

<script>
	import V5Button from '@/components/verify5-ui/V5Button.vue'; // 动态校验
	import moveVerify from '@/components/helang-moveVerify/helang-moveVerify.vue'; // 滑动组件
	import carousel from '@/components/vear-carousel/vear-carousel'; // 轮播图组件
	import comUploadFile from '@/components/comUploadFile/index.vue';
	import markSlideList from '../../components/mark-slide-list/mark-slide-list.vue'
	export default {
		name:'index',
		components: {
			V5Button,
			"move-verify":moveVerify,
			carousel,
			comUploadFile,
			markSlideList
		},
		data() {
			return {
				imgs:[],
				imgUploadUrl:'上传地址',
				title: 'Hello',
				resultData:{},
				imgList: [{
					url: 'https://img9.51tietu.net/pic/2019-091200/vgkpidei2tjvgkpidei2tj.jpg',
					id: 1
				    },{
					url: 'https://img9.51tietu.net/pic/2019-091200/euzekmi5m23euzekmi5m23.jpg',
					id: 2
				    },{
					url: 'https://img9.51tietu.net/pic/2019-091200/143tt0ta4sr143tt0ta4sr.jpg',
					id: 3
				    },{
					url: 'https://img9.51tietu.net/pic/2019-091200/ff1vqwm3q33ff1vqwm3q33.jpg',
					id: 4
				}],
                list : [
					{
						id: 1,
						title: '张三',
						rightDetail: '2019-03-18',
						detail: 'XXXXXXXXXXXXXXXXXXX公司',
						slide_x: 0
					},
					{
						id: 2,
						surname: '李',
						title: '李二',
						rightDetail: '2019-03-17',
						detail: 'XXXXXXXXXXXXXXXXXXX公司',
						slide_x: 0
					}
				],
				buttonList: [
					{
						title: '分享',
						background: '#c4c7cd'
					},
					{
						title: '删除',
						background: '#ff3b32'
					}
				]
			}
		},
		onLoad() {
		},
		methods: {
			// 图片上传结果
			resultUrl(e){
				console.log(e,111)
				if(e){
					obj.imgs.push(e.result[0]); //e为返回的对象
				}
			},
			
			// 删除图片结果
			delImg(e){
				obj.imgs.splice(e,1) //为删除图片数组的下标
			},
			changeMethod(data, button, index){
					console.log('滑动按钮回调', data)
					console.log('滑动按钮回调', button)
				},
			clickMethod(data){
				console.log('点击行回调', data)
			},
			 //获取文件
			getUploadFile(val){
				this.uploadedFilesData = val;
			},
			//删除文件
			deleteFiles(val){
				this.uploadedFilesData = val;
			},
			uploadSuccess(result) {
				if(result.statusCode == 200) {
					//上传成功的回调处理
					uni.showToast({
						title: '上传成功',
						icon: 'none'
					});
				}else{
					uni.showToast({
						title: '上传失败',
						icon: 'none'
					});
				}
			},
            /* 校验结果回调函数 */
			verifyResult(res){
				console.log(res);
				this.resultData = res;
			},
			/* 校验插件重置 */
			verifyReset(){
				this.$refs.verifyElement.reset();
				/* 删除当前页面的数据 */
				this.resultData = {};
			},
			 selectedBanner(item, index) {
					console.log('🥒', item, index)
				}
		}
	}
</script>

<style>
	.content {
		display: flex;
		flex-direction: column;
		align-items: center;
		justify-content: center;
	}

	.logo {
		height: 200rpx;
		width: 200rpx;
		margin-top: 200rpx;
		margin-left: auto;
		margin-right: auto;
		margin-bottom: 50rpx;
	}

	.text-area {
		display: flex;
		justify-content: center;
	}

	.title {
		font-size: 36rpx;
		color: #8f8f94;
	}
</style>
