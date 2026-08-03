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

        <button
          type="button"
          class="show-all-btn"
          @click="showGalleryModal = true"
        >
          <i class="fa-regular fa-images"></i>
          Show all photos
        </button>
      </div>
    </div>
  </div>

  <GalleryModal
    :show="showGalleryModal"
    :photos="photos"
    @close="showGalleryModal = false"
  />
</template>

<script setup>
import { ref } from "vue";
import GalleryModal from "./modals/GalleryModal.vue";

defineProps({
  photos: {
    type: Array,
    required: true,
  },
});

const showGalleryModal = ref(false);
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
  right: max(12px, 0.8333vw);
  bottom: max(12px, 0.8333vw);
  display: inline-flex;
  align-items: center;
  gap: max(7px, 0.4861vw);
  padding: max(9px, 0.625vw) max(16px, 1.1111vw);
  border: max(1px, 0.0694vw) solid rgba(255, 255, 255, 0.3);
  border-radius: max(6px, 0.4167vw);
  background: rgba(20, 20, 20, 0.76);
  backdrop-filter: blur(max(5px, 0.3472vw));
  color: #ffffff;
  font-size: max(13px, 0.9028vw);
  font-weight: 600;
  cursor: pointer;
  transition:
    background 0.25s ease,
    transform 0.25s ease;
}

.show-all-btn:hover {
  background: rgba(12, 24, 81, 0.92);
  transform: translateY(min(-2px, -0.1389vw));
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

@media (max-width: 768px) {
  .container {
    padding: 0 16px;
  }

  .gallery {
    grid-template-columns: 1fr;
    height: auto;
  }

  .gallery-main {
    height: 320px;
  }

  .gallery-grid {
    height: 360px;
  }
}

@media (max-width: 520px) {
  .gallery-main {
    height: 240px;
  }

  .gallery-grid {
    grid-template-columns: 1fr 1fr;
    height: 300px;
  }

  .show-all-btn {
    right: 8px;
    bottom: 8px;
    padding: 8px 11px;
    font-size: 11px;
  }
}
</style>