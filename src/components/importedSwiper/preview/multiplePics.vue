<script setup>
import SwiperCore from 'swiper';
import { Swiper, SwiperSlide } from 'swiper/vue'
import { Navigation, Pagination } from 'swiper/modules';


// import Swiper and modules styles
import 'swiper/css'
import 'swiper/css/navigation'
import 'swiper/css/pagination'

defineProps({
  images: {
    type: Array,
    required: true,
    default: () => []
  }
})

// 定义 Swiper 配置
const swiperModules = [Navigation, Pagination]

</script>

<template>
  <div>
    <swiper
      :modules="swiperModules"
      :pagination="{el: '.swiper-pagination', clickable: true }"
      :navigation="{nextEl: '.swiper-button-next', prevEl: '.swiper-button-prev'}"
    >
      <swiper-slide v-for="(image, index) in images" :key="index">
        <img :src="image" alt="图片" class="swiper-image">
      </swiper-slide>
      <!-- 如果你需要导航按钮 -->
      <div class="swiper-pagination"></div>
      <div class="swiper-button-prev"></div>
      <div class="swiper-button-next"></div>
    </swiper>
  </div>
</template>

<style>
.swiper-image {
  width: 100%; /* 或者你希望的宽度 */
  height: 100%; /* 保持图片比例 */
  object-fit: contain;
}
.swiper {
  width: 100%;
  height: 100%;
}
</style>