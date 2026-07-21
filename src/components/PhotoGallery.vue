<template>
  <div class="container gallery">
    <div class="gallery-main">
      <img :src="photos[0].src" :alt="photos[0].alt" />
    </div>
    <div class="gallery-grid">
      <img :src="photos[1].src" :alt="photos[1].alt" />
      <img :src="photos[2].src" :alt="photos[2].alt" />
      <img :src="photos[3].src" :alt="photos[3].alt" />
      <div class="gallery-last">
        <img :src="photos[4].src" :alt="photos[4].alt" />
        <button class="show-all-btn" @click="$emit('show-all')">Show all photos</button>
      </div>
    </div>
  </div>
</template>

<script setup>
defineProps({
  photos: {
    type: Array,
    required: true,
  },
});
defineEmits(["show-all"]);
</script>

<style scoped>
.container {
  max-width: 1340px;
  margin: 0 auto;
  padding: 0 34px;
}
.gallery {
  display: grid;
  grid-template-columns: 1.28fr 1fr;
  gap: 5px;
  height: 470px;
}
.gallery-main {
  min-height: 0;
  overflow: hidden;
  border-radius: 0px;
}
.gallery-main img {
  width: 100%;
  height: 100%;
  object-fit: cover;
  display: block;
}
.gallery-grid {
  display: grid;
  grid-template-columns: repeat(2, 1fr);
  grid-template-rows: repeat(2, 1fr);
  gap: 5px;
  height: 100%;
}
.gallery-grid img {
  width: 100%;
  height: 100%;
  min-width: 0;
  min-height: 0;
  object-fit: cover;
  border-radius: 0px;
  display: block;
}
.gallery-last {
  position: relative;
  min-height: 0;
  overflow: hidden;
  border-radius: 0px;
}
.show-all-btn {
  position: absolute;
  bottom: 12px;
  right: 12px;
  background: rgba(20, 20, 20, 0.72);
  color: #fff;
  border: none;
  padding: 8px 16px;
  border-radius: 6px;
  font-size: 13px;
  font-weight: 600;
  cursor: pointer;
}
</style>
