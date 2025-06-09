<script setup>
import defaultImage from "./assets/default.jpg";
import axios from "axios";
import {ref} from "vue";
// import JUpload from '@/components/jeecg/JUpload';
import { ElMessage } from 'element-plus'
import 'element-plus/dist/index.css'


let showPopup = ref(false);
let showTwitterPopup = ref(false);
const avatarUrl = ref('');

let videoFormat = ref(undefined);
let fileList = ref([]);

// 上传之前的钩子
const beforeAvatarUpload = (files, fileList) => {
  console.log('beforeAvatarUpload:', files);
  console.log('beforeAvatarUpload:', fileList);
  console.log('beforeAvatarUpload:', videoFormat);
  fileList.push(files);
  console.log('beforeAvatarUpload:', fileList);
  if (videoFormat !== undefined && !videoFormat && fileList.length > 9) {
    ElMessage.error('上传文件的图片数量不能超过 9个!');
    return false;
  } else if (videoFormat !== undefined && videoFormat && fileList.length > 1) {
    ElMessage.error('上传文件的视频数量不能超过 1个!');
    return false;
  }
  files.forEach(file => {
    // 判断是否有别的格式
    const isJPG = file.type === 'image/jpeg' || file.type === 'image/png';
    const isVideo = chkVideoFormat(file.type);
    videoFormat = isVideo;
    const fileSize = file.size / 1024 / 1024;

    if (!isJPG && !isVideo) {
      ElMessage.error('上传文件只能是图片(jpg/png)或者是视频格式(.mp4/.mkv/.ogg)!');
      return false;
      // 判断是否图片里是否夹着其他格式
    } else if (isJPG && isVideo) {
      ElMessage.error('上传文件只能是纯图片(jpg/png)或者是纯视频格式(.mp4/.mkv/.ogg)!');
      return false;
    } else {
      if (isJPG) {
        if (fileSize > 50) {
          ElMessage.error('上传文件的图片大小不能超过 50MB!');
          return false;
        }
      } else if (isVideo) {
        if (fileSize > 300) {
          ElMessage.error('上传文件的视频大小不能超过 300MB!')
        }
      }
    }
  });
  return true;
}

const chkVideoFormat = (fileType) => {
  return fileType === 'video/mp4' || fileType === 'video/webm' || fileType === 'video/ogg';
}

// 上传成功的处理函数
const handleAvatarSuccess = (response) => {
  console.log('上传成功:', response);
  avatarUrl.value = response.url; // 假设后端返回的数据中包含 url 字段
  ElMessage.success('上传成功');
  // showPopup.value = false;
}
// 上传失败的处理函数
const handleAvatarError = () => {
  ElMessage.error('上传失败，请重试');
}

let shangchuanpeizhi = {
  headers: {
    'Content-Type': 'multipart/form-data'
  }
}

function thumbnailChange(e) {
  console.log(e.target.files[0])
  let file = e.target.files[0]
  let reader = new FileReader()
  reader.readAsDataURL(file)
  reader.onload = function(e) {
    console.log(e.target.result)
    // 预览thumbnail image
    let img = document.querySelector('.popup-section1 img')
    img.src = e.target.result;

    const formData = new FormData();formData.append('file', file);
    console.log('newFile:{}', formData)
    // upload
    axios.post('http://localhost:8080/api/v1/main/baby/uploadThumbnail', formData, {
        headers: {
          'Content-Type': 'multipart/form-data'
        }
      })
        .then((r) => {

          console.log(r);
        });
  }
}

function uploadThumbnail(e) {
  console.log("showPopup:", showPopup.value);
  console.log("showPopup:", !showPopup.value);
  return showPopup.value = !showPopup.value;
}
// +
function uploadTwitter(e) {
  fileList = ref([]);
  return showTwitterPopup.value = !showTwitterPopup.value;
}

function upload() {

}

</script>

<template>
  <header>
      <div class="header">
        <span style="margin-right: 5px">个人设置</span>
        <img src="./assets/setting.svg" alt="我是个icon" style="width: 20px;height: 20px">
      </div>
  </header>
  <main>
    <div class="intro-section">
      <div class="section1">
        <img :src="avatarUrl" alt="" class="img" @click="uploadThumbnail" @error="e=> {e.target.src = defaultImage}">
      </div>
      <div class="section2">
        <div class="img">
          <img src="./assets/网页素材-枫.png" style="width:100%;height:100%;object-fit: fill">
        </div>
  <!--      <div><span>父母寄语</span></div>-->
      </div>
      <div class="section3">大儿的年龄</div>
    </div>

    <!-- 分割线 -->
    <div style="border: 1px solid #ccc"></div>
    <div class="title">
      <span style="font-size: large">大事记</span>
      <span style="width: 5%; height: 5%"><img src="./assets/加号.svg" style="object-fit: fill; width: 100%;height: 100%" @click="uploadTwitter"></span>
    </div>
    <div class="content">
      <!--    循环div-->
      <div class="part">动态1</div>
      <div class="part">动态2</div>
      <div class="part">动态3</div>
    </div>
    <div class="footer">
      <div class="footer-img">
        <img src="./assets/网页素材-向日葵2.png" style="width:100%;height:100%;object-fit: fill" >
      </div>
    </div>
  <!-- pop up  -->
  <div v-if="showPopup" class="popup">
    <div class="popup-content">
      <span @click="uploadThumbnail" class="popup-close">&times;</span>
      <div class="popup-section1">
        <div class="img-preview">
          <img :src="avatarUrl" alt="" class="popup-img" @error="e => {e.target.src=defaultImage}">
        </div>
        &nbsp;
        &nbsp;
        <div>
          <el-upload
              class="avatar-uploader"
              action="http://localhost:8080/api/v1/main/baby/uploadThumbnail"
              :show-file-list="true"
              :on-success="handleAvatarSuccess"
              :on-error="handleAvatarError"
              :before-upload="beforeAvatarUpload"
              :headers="shangchuanpeizhi.headers"
              multiple
              :file-list="fileList"
          >
            <el-button type="primary">点击上传头像</el-button>
            <template #tip>
              <div class="el-upload__tip">
                只能上传 jpg/png 文件，且不超过 50MB
              </div>
            </template>
          </el-upload>
        </div>
      </div>
    </div>
  </div>

    <!-- not avatar pop up -->
  <div v-if="showTwitterPopup" class="popup">
    <div class="popup-content">
      <span @click="uploadTwitter" class="popup-close">&times;</span>
      <div class="popup-section1">
        <div class="img-preview">
          <img :src="avatarUrl" alt="" class="popup-img" @error="e => {e.target.src=defaultImage}">
        </div>
        &nbsp;
        &nbsp;
        <div>
          <el-upload
              class="avatar-uploader"
              action="http://localhost:8080/api/v1/main/baby/uploadThumbnail"
              :show-file-list="true"
              :on-success="handleAvatarSuccess"
              :on-error="handleAvatarError"
              :before-upload="beforeAvatarUpload"
              :headers="shangchuanpeizhi.headers"
              multiple
              :file-list="fileList"
          >
            <el-button type="primary">点击上传图片/视频</el-button>
            <template #tip>
              <div class="el-upload__tip">
                只能上传 jpg/png 图片，且不超过 50MB，或mp4/mkv/ogg 视频，且不超过300M
              </div>
            </template>
          </el-upload>
        </div>
      </div>
    </div>
  </div>
  </main>
</template>

<style>
.header {
  display: flex;
  justify-content: flex-end;
  align-items: center;
  padding: 10px 10px 0 0;
  border-bottom: 1px solid #ccc;
}
.intro-section {
  display: flex;
  align-items: center;
  height: 225px;
}

.intro-section .section1 {
  width: 15%;
  height: 100%;
  background-color: #fff;
  border: 1px solid #fff;
  border-radius: 50%;
}
.intro-section .section1 .img {
  width: 100%;
  height: 100%;
  background-color: #fff;
  border: 1px solid #ccc;
  border-radius: 50%;
}
.intro-section .section2 {
  width: 65%;
  height: 100%;
  background-color: #fff;

  display: flex;

}

.intro-section .section2 .img {
  width: 100%;
  height: 100%;
}
.intro-section .section3 {
  width: 15%;
  height: 100%;
  background-color: #fff;

  display: flex;
  justify-content: center;
  align-items: center;
}
.title {
  display: flex;
  flex-direction: row;
  justify-content: space-between;
  padding: 10px 10%;
}
.content {
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
}
.content .part {
  display: flex;
  justify-content: center;
  align-items: center;
  height: 140px;
  width: 80%;
  background-color: #fff;
  border: 1px solid #ccc;
}
.popup {

  position: fixed; /* 固定定位 */
  z-index: 1; /* 位于顶层 */
  left: 0;
  top: 0;
  width: 100%; /* 全宽 */
  height: 100%; /* 全高 */

  background-color: rgba(0,0,0,0.4); /* 背景颜色，半透明 */
}
.popup-content {
  background-color: #fefefe;
  margin: 15% auto; /* 15% 从顶部和自动水平居中 */
  padding: 20px;
  border: 1px solid #888;
  width: 80%; /* 宽度 */
  height: 70%;
}
.popup-section1 {
  width: 90%;
  height: 90%;
  text-align: center;
}
.popup-close {
  color: #aaa;
  float: right;
  font-size: 28px;
  font-weight: bold;
}
.popup-close:hover,
.popup-close:focus {
  color: black;
  text-decoration: none;
  cursor: pointer;
}
.img-preview {
display: inline-block;
  width: 25%;
  height: 50%;
  background-color: #fff;
  border: 1px solid #ccc;
}
.popup-img {
  width: 100%;
  height: 100%;
}
.avatar-uploader {
  text-align: center;
  margin-top: 20px;
}
.el-upload__tip {
  font-size: 12px;
  color: #666;
  margin-top: 10px;
}
.upload-container {
  display: flex;
  flex-direction: column;
  align-items: center;
  margin-top: 20px;
}
.footer{
  display: flex;
  justify-content: right;
}
.footer-img{
  width: 25%;
  height: 225px;
}
</style>
