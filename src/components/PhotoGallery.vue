<template>
  <div class="container gallery">
    <div class="gallery-main">
      <img :src="photos[0].src" :alt="photos[0].alt" />
    </div>
    <div class="gallery-grid">
      <div class="gallery-item">
        <img :src="photos[1].src" :alt="photos[1].alt" />
      </div>

      <div class="gallery-item">
        <img :src="photos[2].src" :alt="photos[2].alt" />
      </div>

      <div class="gallery-item">
        <img :src="photos[3].src" :alt="photos[3].alt" />
      </div>

      <div class="gallery-last">
        <img :src="photos[4].src" :alt="photos[4].alt" />
        <button class="show-all-btn">Show all photos</button>
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
  max-width: max(1340px, 93.0556vw);
  margin: 0 auto;
  padding: 0 max(34px, 2.3611vw);
}
.gallery {
  display: grid;
  grid-template-columns: 1.28fr 1fr;
  gap: max(5px, 0.3472vw);
  height: max(470px, 32.6389vw);
}
.gallery-main {
  min-height: 0;
  overflow: hidden;
  border-radius: 0;
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
  gap: max(5px, 0.3472vw);
  height: 100%;
}
.gallery-grid img {
  width: 100%;
  height: 100%;
  min-width: 0;
  min-height: 0;
  object-fit: cover;
  border-radius: 0;
  display: block;
}
.gallery-last {
  position: relative;
  min-height: 0;
  overflow: hidden;
  border-radius: 0;
}
.show-all-btn {
  position: absolute;
  bottom: max(12px, 0.8333vw);
  right: max(12px, 0.8333vw);
  background: rgba(20, 20, 20, 0.72);
  color: #fff;
  border: none;
  padding: max(8px, 0.5556vw) max(16px, 1.1111vw);
  border-radius: max(6px, 0.4167vw);
  font-size: max(13px, 0.9028vw);
  font-weight: 600;
  cursor: pointer;
}

.gallery-item,
.gallery-main,
.gallery-last {
  overflow: hidden;
}

.gallery img {
  transition: transform 0.4s ease;
}

.gallery-item:hover img,
.gallery-main:hover img,
.gallery-last:hover img {
  transform: scale(1.08);
}
</style>
