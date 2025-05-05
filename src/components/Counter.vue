<script setup lang="ts">
import { ref, useTemplateRef, watch } from "vue";
import videoSrc from "../assets/video.mp4";
import { onClickOutside, useStorage } from "@vueuse/core";

const count = useStorage("count", 0);
const videoRef = useTemplateRef<HTMLVideoElement>("video");
const isVideoPlaying = ref(true);

watch(count, (newCount) => {
  if (newCount === 30) {
    isVideoPlaying.value = true;
    videoRef.value?.play();
  }
});

const handleInc = () => {
  count.value += 1;
};

const handleDecr = () => {
  if (count.value > 0) {
    count.value -= 1;
  }
};

const toggleVideo = () => {
  if (videoRef.value) {
    if (isVideoPlaying.value) {
      videoRef.value.pause();
    } else {
      videoRef.value.play();
    }
    isVideoPlaying.value = !isVideoPlaying.value;
  }
};

const closeVideo = () => {
  count.value = 0;
  isVideoPlaying.value = false;
  videoRef.value?.pause();
};

onClickOutside(videoRef, () => {
  if (videoRef.value && isVideoPlaying.value) {
    videoRef.value.pause();
    isVideoPlaying.value = false;
  }
});

const resetCount = () => {
  count.value = 0;
  isVideoPlaying.value = false;
  videoRef.value?.pause();
};
</script>

<template>
  <section class="count_container">
    <button @click="handleDecr" class="rest_count">-</button>
    <p class="counter">{{ count }}</p>
    <button @click="handleInc" class="add_count">+</button>
    <button @click="resetCount" class="reset_count">Reset</button>
    <transition name="fade">
      <div v-if="count === 30" class="video_container">
        <video
          ref="video"
          :src="videoSrc"
          class="video"
          preload="auto"
          @click="toggleVideo"
          autoplay
          loop
        ></video>
        <button class="close_video" @click="closeVideo">Cerrar</button>
      </div>
    </transition>
  </section>
</template>

<style scoped>
.video_container {
  position: relative;
}
.close_video {
  position: absolute;
  top: -180px;
  right: 30;
  background-color: rgba(58, 59, 59, 0.8);
  color: rgb(231, 231, 231);
  border-radius: 5px;
  padding: 5px 10px;
  cursor: pointer;
  transition: background-color 0.2s ease-in;
}
.close_video:hover {
  background-color: rgb(79, 80, 80);
}

.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.5s ease;
}
.fade-enter-from,
.fade-leave-to {
  opacity: 0;
}

/* Counter animation */
.count-value {
  display: inline-block;
  transition: transform 0.2s ease;
}
.count-value:active {
  transform: scale(1.2);
}

.reset_count {
  background-color: rgb(58, 59, 59);
  color: rgb(231, 231, 231);
  width: 3rem;
  height: 2rem;
  font-size: 16px;
  border-radius: 5px;
  cursor: pointer;
  transition: background-color 0.2s ease-in;
}
.reset_count:hover {
  background-color: rgb(79, 80, 80);
}
</style>
