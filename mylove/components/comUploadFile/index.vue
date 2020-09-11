<template>
    <view class="upload" >
        <view >上传附件</view>
        <view class="image file">
            <view v-if="fileList.length && fileList.length>0">
                <view v-for="(item, key) of fileList" :key="key">
                    <view class="left" @longtap="longTapDelete" @tap="downloadFile(item)">
                        <image v-if="item.fileType == 'doc' || item.fileType == 'docx'" src="/static/logo.png" />
                        <image v-else-if="item.fileType == 'pdf'" src="/static/logo.png" />
                        <image v-else-if="item.fileType == 'ppt' || item.fileType == 'pptx'" src="/static/logo.png" />
                        <image v-else-if="item.fileType == 'txt'" src="/static/logo.png" />
                        <image v-else-if="item.fileType == 'rar' || item.fileType == 'zip'" src="/static/logo.png" />
                        <image v-else-if="item.fileType == 'xlsx' || item.fileType == 'xls' || item.fileType == 'csv'" src="/static/logo.png" />
                        <image v-else src="/static/logo.png" />
                        <view class="info">
                            <text class="name">{{item.fileName}}</text>
                            <text class="size">{{item.fileSize}}</text>
                        </view>
                        <text v-if="showFile"  class="icon iconshanchu2"  @click="deletes(item.id, key)"></text>
                     </view>
                </view>
		    </view>	
			<view class="add">
			    <image src="@/static/logo.png" style="height: 180rpx;width: 180rpx;" ></image>
			    <input type="file" ref="uploadFile" @change="handleChange" multiple="multiple">
			    <text v-if="fileList.length==0">单个文件不超过100M</text>
			</view>
        </view>
    </view>
</template>
<script>

export default {
    name:'uploadFile',
    data() {
        return {
            fileList: [],
            showFile: false,
            downloadOriginalFile: null, //路径
        }
    },
    computed: {
        proApi() {
            return Global && Global.proApi || '';
        }
    },
    methods:{
        //重置
        clearData(){
            this.$refs.uploadFile.value = '';
        },
        clearFileList(){
            this.fileList.length=0;
        },
        //长按显示/隐藏删除图标
        longTapDelete() {
            this.showFile = !this.showFile;
        },
        //删除附件
        deletes(id, key){
            let basUrl = this.$api.file.delAttachment;
            let obj = {
                type: 7,
                fileId: id
            };
            this.$ajax.del(basUrl,obj).then(res=>{
                if(res.success)
                {
                    this.showFile = false;
                    this.fileList.splice(key, 1);
                    this.$emit('removeFile',this.fileList);
                    this.clearData();
                }else{
                    vm.$showloading();
                    this.$toast(res.message);
                }
            }).catch(err=>{
                vm.$showloading();
                this.$toast(err);
            })
        },
        //点击上传附件
        async handleChange(event){
            let data = new FormData();
            let basUrl = this.$api.file.normalUploadFile;
            let list = event.target.files;
            this.showFile = false;
            data.append('type',7);
            data.append('fileId','');

            for (let i=0;i<list.length;i++)
            {
                if(list[i].size < 100*1024*1024)
                {
                    data.set('upfile',list[i]);
                    data.set("fileName",list[i].name);
                    //文件类型
                    let typeName = list[i].name.substring(list[i].name.lastIndexOf(".") + 1).toLowerCase();
                    let a = await this.uploadFile(basUrl,data,i,typeName);
                    this.clearData();
                }else {
                    this.clearData();
                    vm.$toast('文件大小不能超过100M');
                    return false;
                }
            }
        },
        //上传附件
        uploadFile(basUrl,data,i,typeName){
            // 上传文件
            vm.$showloading('正在上传中...');
            this.$ajax.post(basUrl, data, {}, 'multipart/form-data').then(res => {
                if (res.code == 200 && res.success)
                {
                    let file = res.result;
                    if(file.fileName.length > 13){
                        file.fileName = file.fileName.substr(0,14) + '...'
                    }
                    file.fileType = typeName;
                    this.fileList.push({fileType: file.fileType, id:file.id, url: file.url, fileSize: file.fileSize, fileName:file.fileName});
                    this.$emit('sendFiles',this.fileList);
                    vm.$showloading();
                    this.$toast(res.message,1000);
                }
                else {
                    vm.$showloading();
                    this.$toast(res.message,2000);
                }
            }).catch(e => {
                vm.$showloading();
                this.$toast(e,2000);
            });
        },
        //下载文件
        downloadFile(item) {
            let load = this.$global.proApi + this.$api.file.downloadFile;
            let token = this.$store.getters.accessToken;
            let id = item.id;
            let fileName = item.fileName;
            this.showFile = false;

            if(id == '' && id == null)
            {
                this.$toast('未找到此文件',2000);
            }
            else {
                let url = load + id + '?accessToken=' + token;
                this.$download(url, fileName);
            }
        }
    }
}
</script>
<style lang="scss" src="./style.scss"></style>