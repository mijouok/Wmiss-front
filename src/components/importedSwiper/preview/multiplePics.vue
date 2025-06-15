<script setup>
import {ref} from "vue";
import {computed} from "vue";
import SwiperCore from 'swiper';
import { Swiper, SwiperSlide } from 'swiper/vue'
import { Navigation, Pagination } from 'swiper/modules';
// import Swiper and modules styles
import 'swiper/css'
import 'swiper/css/navigation'
import 'swiper/css/pagination'

const props = defineProps({
  images: {
    type: Array,
    required: true,
    default: () => []
  }
})
// 定义 Swiper 配置
const swiperModules = [Navigation, Pagination]
const isHovered = ref(false)
const mousePos = ref({ x: 0, y: 0 })
const currentImage = ref('')
const swiperInstance = ref(null)
const zoomLevel = 2 // 放大倍数

// 处理滑块变化
const handleSlideChange = (swiper) => {
  currentImage.value = props.images[swiper.activeIndex || 0]
}

const onSwiper = (swiper) => {
  swiperInstance.value = swiper
  console.log("Swiper instance:", swiper)
}


const handleMouseEnter = () => {
  isHovered.value = true
}

const handleMouseLeave = () => {
  isHovered.value = false
}
//
const handleMouseMove = (event, image) => {
  const rect = event.currentTarget.getBoundingClientRect()
  console.log("handleMouseMove:img width:",event.currentTarget.width);
  console.log("handleMouseMove:img real width:",event.currentTarget.naturalWidth);
  console.log("handleMouseMove:rect width:",event.currentTarget.getBoundingClientRect().width);
  mousePos.value = {
    x: event.clientX - rect.left,
    y: event.clientY - rect.top
  }
  console.log("mousePos:", mousePos.value);
}
// 计算放大图片的位置
const zoomedImageStyle = computed(() => {
  return {
    transform: `translate(-${mousePos.value.x * zoomLevel}px, -${mousePos.value.y * zoomLevel}px) scale(${zoomLevel})`
  }
})
// 计算放大层的位置和大小
const zoomStyle = computed(() => {
  return {
    left: `${mousePos.value.x}px`,
    top: `${mousePos.value.y}px`,
  }
})

</script>

<template>
  <div>
    <swiper
      :modules="swiperModules"
      :pagination="{el: '.swiper-pagination', clickable: true }"
      :navigation="{nextEl: '.swiper-button-next', prevEl: '.swiper-button-prev'}"
      :loop="true"
      @swiper="onSwiper"
      @slide-change="handleSlideChange"
    >
      <swiper-slide v-for="(image, index) in images" :key="index">
        <div
          class="image-container"
        >
          <img :src="image" alt="图片" class="swiper-image" @mouseover="handleMouseMove"
               @mouseenter="handleMouseEnter"
               @mouseleave="handleMouseLeave">
          <div v-show="isHovered" class="zoom-layer" :style="zoomStyle">
            <img :src="currentImage" alt="放大图" class="zoomed-image" :style="zoomedImageStyle">
          </div>
        </div>
      </swiper-slide>
      <!-- 如果你需要导航按钮 -->
      <div class="swiper-pagination"></div>
      <div class="swiper-button-prev"></div>
      <div class="swiper-button-next"></div>
    </swiper>
  </div>
</template>

<style>
.swiper-container {
  width: 100%;
  max-width: 800px;
  margin: 0 auto;
}
.swiper {
  width: 100%;
  height: 100%;
}
.swiper-image {
  width: auto; /* ！！使标签适应图片内容 */
  height: 100%;
  object-fit: contain;
}
.image-container {
  position: relative;
  width: auto;
  height: 100%;
  overflow: hidden;
}
.zoom-layer {
  position: absolute;
  width: 350px;
  height: 350px;
  overflow: hidden;
  pointer-events: none; /* 防止放大层影响鼠标事件 */
  box-shadow: 0 0 10px rgba(0,0,0,0.3);
  transform: translate(-50%, -50%); /* 居中对齐鼠标位置 */
  z-index: 10;
}
.zoomed-image {
  position: absolute;
  left: 0;
  top: 0;
  width: 100%;
  height: 100%;
  object-fit: contain;
}
</style>