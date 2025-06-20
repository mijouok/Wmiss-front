<script setup>
import {ref} from "vue";
import {computed} from "vue";
import SwiperCore from 'swiper';
import {Swiper, SwiperSlide} from 'swiper/vue'
import {Navigation, Pagination, EffectCreative} from 'swiper/modules';
// import Swiper and modules styles
import 'swiper/css'
import 'swiper/css/navigation'
import 'swiper/css/pagination'
import 'swiper/css/effect-creative'

const props = defineProps({
  images: {
    type: Array,
    required: true,
    default: () => []
  }
})
// 定义 Swiper 配置
const swiperModules = [Navigation, Pagination, EffectCreative]
const isHovered = ref(false)
const mousePos = ref({x: 0, y: 0})
const currentImage = ref('')
const swiperInstance = ref(null)
const zoomLevel = 2 // 放大倍数
const auto = ref('auto');

// 处理滑块变化
const handleSlideChange = (swiper) => {
  currentImage.value = props.images[swiper.activeIndex || 0]
}

const onSwiper = (swiper) => {
  swiperInstance.value = swiper
  console.log("Swiper instance:", swiper)
}

// const certifySwiper = new SwiperCore('#image-swiper .swiper', {
//   watchSlidesProgress: true,
//   slidesPerView: 'auto',
//   centeredSlides: true,
//   loop: true,
//   loopedSlides: 5,
//   autoplay: true,
//   navigation: {
//     nextEl: '.swiper-button-next',
//     prevEl: '.swiper-button-prev',
//   },
//   pagination: {
//     el: '.swiper-pagination',
//     //clickable :true,
//   },
//   on: {
//     progress: function(progress) {
//       for (let i = 0; i < this.slides.length; i++) {
//         let slide = this.slides.eq(i);
//         let slideProgress = this.slides[i].progress;
//         let modify = 1;
//         if (Math.abs(slideProgress) > 1) {
//           modify = (Math.abs(slideProgress) - 1) * 0.3 + 1;
//         }
//         let translate = slideProgress * modify * 260 + 'px';
//         let scale = 1 - Math.abs(slideProgress) / 5;
//         let zIndex = 999 - Math.abs(Math.round(10 * slideProgress));
//         slide.transform('translateX(' + translate + ') scale(' + scale + ')');
//         slide.css('zIndex', zIndex);
//         slide.css('opacity', 1);
//         if (Math.abs(slideProgress) > 3) {
//           slide.css('opacity', 0);
//         }
//       }
//     },
//     setTransition: function(swiper, transition) {
//       for (let i = 0; i < this.slides.length; i++) {
//         let slide = this.slides.eq(i)
//         slide.transition(transition);
//       }
//
//     }
//   }
//
// })

const onProgress = (swiper, progress) => {

  for (let i = 0; i < swiper.slides.length; i++) {
    let slide = swiper.slides[i];
    let slideProgress = swiper.slides[i].progress;
    console.log('onProgress: slide:', slide);
    console.log('onProgress: slideProgress:', slideProgress);
    let modify = 1;
    if (Math.abs(slideProgress) > 1) {
      modify = (Math.abs(slideProgress) - 1) * 0.3 + 1;
    }
    let translate = slideProgress * modify * 260 + 'px';
    let scale = 1 - Math.abs(slideProgress) / (props.images.length);
    let zIndex = 999 - Math.abs(Math.round(10 * slideProgress));
    slide.style.transform = 'translateX(' + translate + ') scale(' + scale + ')';
    slide.style.zIndex = zIndex;
    slide.style.opacity = 1;
    if (Math.abs(slideProgress) > 3) {//
      slide.style.opacity = 0;
    }
  }
}

const onSetTrans = (swiper, transition) => {
  for (let i = 0; i < swiper.slides.length; i++) {
    let slide = swiper.slides[i]
    slide.style.transition = transition;
  }
}


</script>

<template>
  <div id="image-swiper">
    <swiper
        :modules="swiperModules"
        :loop="true"
        :slidesPerView="auto"
        :navigation="{
        nextEl: '.swiper-button-next',
        prevEl: '.swiper-button-prev',
      }"
        :pagination=" {
        el: '.swiper-pagination',
        clickable :true,
       }"
        :effect="'creative'"
        :creative-effect="{
        prev: {
          scale: 0.8,
          translate: [-170, 0, 0],

        },
        next: {
          scale: 0.8,
          translate: [170, 0, 0],
        }
      }"
        @slide-change="handleSlideChange"
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
  box-shadow: 0 0 10px rgba(0, 0, 0, 0.3);
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