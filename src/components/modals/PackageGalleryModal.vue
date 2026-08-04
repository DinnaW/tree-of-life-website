<template>
  <Teleport to="body">
    <Transition name="gallery-modal">
      <div
        v-if="show && packageData"
        class="gallery-overlay"
        @click.self="closeModal"
      >
        <section
          class="gallery-popup"
          role="dialog"
          aria-modal="true"
          :aria-label="`${packageData.title} gallery`"
        >
          <!-- Header -->
          <header class="gallery-header">
            <div class="gallery-heading">
              <span class="gallery-category">
                {{ packageData.category }}
              </span>

              <h2>{{ packageData.title }}</h2>

              <p>
                Explore the experience through our selected images.
              </p>
            </div>

            <button
              type="button"
              class="gallery-close"
              aria-label="Close gallery"
              @click="closeModal"
            >
              <Icon icon="lucide:x" />
            </button>
          </header>

          <!-- Gallery -->
          <div
            v-if="packageImages.length"
            class="gallery-layout"
          >
            <!-- Large feature image -->
            <button
              type="button"
              class="gallery-main-image"
              @click="openPreview(0)"
            >
              <img
                :src="packageImages[0]"
                :alt="`${packageData.title} main gallery image`"
              />

              <span class="image-gradient"></span>

              <span class="main-image-action">
                <Icon icon="lucide:maximize-2" />
                View image
              </span>
            </button>

            <!-- Side images -->
            <div class="gallery-side">
              <button
                v-for="(image, index) in sideImages"
                :key="`${image}-${index}`"
                type="button"
                class="gallery-side-image"
                @click="openPreview(index + 1)"
              >
                <img
                  :src="image"
                  :alt="`${packageData.title} gallery image ${index + 2}`"
                />

                <span class="side-image-overlay"></span>

                <span
                  v-if="
                    index === sideImages.length - 1 &&
                    packageImages.length > 3
                  "
                  class="more-images"
                >
                  <Icon icon="lucide:images" />

                  <strong>
                    +{{ packageImages.length - 3 }}
                  </strong>

                  <small>View all</small>
                </span>
              </button>
            </div>
          </div>

          <!-- Thumbnail strip -->
          <div
            v-if="packageImages.length"
            class="thumbnail-strip"
          >
            <button
              v-for="(image, index) in packageImages"
              :key="`thumbnail-${image}-${index}`"
              type="button"
              class="thumbnail-button"
              @click="openPreview(index)"
            >
              <img
                :src="image"
                :alt="`${packageData.title} thumbnail ${index + 1}`"
              />

              <span>
                {{ index + 1 }}
              </span>
            </button>
          </div>

          <!-- Empty state -->
          <div
            v-else
            class="empty-gallery"
          >
            <span class="empty-icon">
              <Icon icon="lucide:images" />
            </span>

            <h3>No gallery images</h3>

            <p>
              Images for this package will be added soon.
            </p>
          </div>
        </section>

        <!-- Full screen preview -->
        <Transition name="preview-modal">
          <div
            v-if="previewImage"
            class="preview-overlay"
            @click.self="closePreview"
          >
            <header class="preview-header">
              <div>
                <span>{{ packageData.category }}</span>
                <strong>{{ packageData.title }}</strong>
              </div>

              <button
                type="button"
                class="preview-close"
                aria-label="Close image preview"
                @click="closePreview"
              >
                <Icon icon="lucide:x" />
              </button>
            </header>

            <button
              v-if="packageImages.length > 1"
              type="button"
              class="preview-arrow preview-arrow-left"
              aria-label="Previous image"
              @click.stop="previousImage"
            >
              <Icon icon="lucide:chevron-left" />
            </button>

            <div class="preview-image-container">
              <Transition
                name="image-change"
                mode="out-in"
              >
                <img
                  :key="previewImage"
                  :src="previewImage"
                  :alt="`${packageData.title} image ${previewIndex + 1}`"
                />
              </Transition>

              <span class="preview-counter">
                {{ previewIndex + 1 }} / {{ packageImages.length }}
              </span>
            </div>

            <button
              v-if="packageImages.length > 1"
              type="button"
              class="preview-arrow preview-arrow-right"
              aria-label="Next image"
              @click.stop="nextImage"
            >
              <Icon icon="lucide:chevron-right" />
            </button>

            <div class="preview-thumbnails">
              <button
                v-for="(image, index) in packageImages"
                :key="`preview-${image}-${index}`"
                type="button"
                class="preview-thumbnail"
                :class="{ active: previewIndex === index }"
                @click.stop="previewIndex = index"
              >
                <img
                  :src="image"
                  :alt="`${packageData.title} preview thumbnail ${index + 1}`"
                />
              </button>
            </div>
          </div>
        </Transition>
      </div>
    </Transition>
  </Teleport>
</template>

<script setup>
import {
  computed,
  onBeforeUnmount,
  ref,
  watch
} from "vue";

import { Icon } from "@iconify/vue";

const props = defineProps({
  show: {
    type: Boolean,
    default: false
  },

  packageData: {
    type: Object,
    default: null
  }
});

const emit = defineEmits(["close"]);

const previewIndex = ref(null);

const packageImages = computed(() => {
  return props.packageData?.gallery || [];
});

const sideImages = computed(() => {
  return packageImages.value.slice(1, 3);
});

const previewImage = computed(() => {
  if (previewIndex.value === null) {
    return null;
  }

  return packageImages.value[previewIndex.value] || null;
});

function openPreview(index) {
  previewIndex.value = index;
}

function closePreview() {
  previewIndex.value = null;
}

function closeModal() {
  if (previewImage.value) {
    closePreview();
    return;
  }

  emit("close");
}

function nextImage() {
  if (!packageImages.value.length) return;

  previewIndex.value =
    (previewIndex.value + 1) % packageImages.value.length;
}

function previousImage() {
  if (!packageImages.value.length) return;

  previewIndex.value =
    (
      previewIndex.value -
      1 +
      packageImages.value.length
    ) % packageImages.value.length;
}

function handleKeydown(event) {
  if (!props.show) return;

  if (event.key === "Escape") {
    closeModal();
  }

  if (previewImage.value && event.key === "ArrowRight") {
    nextImage();
  }

  if (previewImage.value && event.key === "ArrowLeft") {
    previousImage();
  }
}

watch(
  () => props.show,
  (isOpen) => {
    if (isOpen) {
      document.body.style.overflow = "hidden";
      window.addEventListener("keydown", handleKeydown);
    } else {
      document.body.style.overflow = "";
      window.removeEventListener("keydown", handleKeydown);
      closePreview();
    }
  }
);

watch(
  () => props.packageData,
  () => {
    closePreview();
  }
);

onBeforeUnmount(() => {
  document.body.style.overflow = "";
  window.removeEventListener("keydown", handleKeydown);
});
</script>

<style scoped>
* {
  box-sizing: border-box;
  font-family: "Figtree", sans-serif;
}

/* Overlay */

.gallery-overlay {
  position: fixed;
  inset: 0;
  z-index: 9999;

  display: flex;
  align-items: center;
  justify-content: center;

  padding: clamp(14px, 2vw, 30px);

  background: rgba(3, 13, 29, 0.72);
  backdrop-filter: blur(12px);
}

/* Main popup */

.gallery-popup {
  width: min(94vw, 1250px);
  max-height: 91vh;

  display: flex;
  flex-direction: column;
  overflow: hidden;

  border: 1px solid rgba(255, 255, 255, 0.55);
  border-radius: 24px;

  background: #ffffff;

  box-shadow:
    0 35px 100px rgba(0, 0, 0, 0.34),
    0 5px 20px rgba(0, 0, 0, 0.08);
}

/* Header */

.gallery-header {
  position: relative;

  display: flex;
  align-items: center;
  justify-content: space-between;
  gap: 30px;

  padding: 24px 30px 20px;

  border-bottom: 1px solid #edf1f6;
}

.gallery-heading {
  min-width: 0;
}

.gallery-category {
  display: block;

  margin-bottom: 6px;

  color: #2476c8;
  font-size: 10px;
  font-weight: 700;
  letter-spacing: 1.6px;
  text-transform: uppercase;
}

.gallery-heading h2 {
  margin: 0;

  color: #1a51ad;
  font-size: clamp(22px, 2vw, 30px);
  font-weight: 600;
  line-height: 1.2;
}

.gallery-heading p {
  margin: 7px 0 0;

  color: #788394;
  font-size: 13px;
  line-height: 1.6;
}

.gallery-close {
  width: 42px;
  height: 42px;

  display: inline-flex;
  align-items: center;
  justify-content: center;
  flex: 0 0 auto;

  padding: 0;

  border: 1px solid #e4eaf1;
  border-radius: 50%;

  background: #f8fafc;
  color: #425066;

  font-size: 19px;
  cursor: pointer;

  transition:
    background 0.2s ease,
    color 0.2s ease,
    transform 0.2s ease;
}

.gallery-close:hover {
  background: #123e76;
  color: #ffffff;
  transform: rotate(5deg);
}

/* Image layout */

.gallery-layout {
  display: grid;
  grid-template-columns: 1.65fr 0.75fr;
  gap: 10px;

  min-height: 0;
  padding: 16px 16px 10px;
}

.gallery-main-image,
.gallery-side-image {
  position: relative;

  overflow: hidden;
  padding: 0;
  border: 0;

  background: #e9eef4;
  cursor: pointer;
}

.gallery-main-image {
  min-height: 440px;
  border-radius: 17px 7px 7px 17px;
}

.gallery-main-image img,
.gallery-side-image img {
  width: 100%;
  height: 100%;

  display: block;
  object-fit: cover;

  transition: transform 0.55s ease;
}

.gallery-main-image:hover img,
.gallery-side-image:hover img {
  transform: scale(1.045);
}

.image-gradient {
  position: absolute;
  inset: 0;

  background:
    linear-gradient(
      180deg,
      transparent 52%,
      rgba(4, 17, 35, 0.62) 100%
    );
}

.main-image-action {
  position: absolute;
  left: 20px;
  bottom: 20px;

  display: inline-flex;
  align-items: center;
  gap: 8px;

  padding: 10px 14px;

  border: 1px solid rgba(255, 255, 255, 0.4);
  border-radius: 30px;

  background: rgba(255, 255, 255, 0.95);
  color: #123e76;

  font-size: 12px;
  font-weight: 600;

  box-shadow: 0 10px 25px rgba(0, 0, 0, 0.18);
}

.gallery-side {
  display: grid;
  grid-template-rows: repeat(2, minmax(0, 1fr));
  gap: 10px;

  min-height: 0;
}

.gallery-side-image:first-child {
  border-radius: 7px 17px 7px 7px;
}

.gallery-side-image:last-child {
  border-radius: 7px 7px 17px 7px;
}

.side-image-overlay {
  position: absolute;
  inset: 0;

  background: rgba(4, 17, 35, 0);
  transition: background 0.3s ease;
}

.gallery-side-image:hover .side-image-overlay {
  background: rgba(4, 17, 35, 0.12);
}

.more-images {
  position: absolute;
  inset: 0;

  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;

  background: rgba(4, 18, 39, 0.6);
  color: #ffffff;

  backdrop-filter: blur(3px);
}

.more-images svg {
  margin-bottom: 7px;
  font-size: 25px;
}

.more-images strong {
  font-size: 25px;
  font-weight: 500;
}

.more-images small {
  margin-top: 3px;
  font-size: 11px;
  letter-spacing: 0.4px;
}

/* Bottom thumbnails */

.thumbnail-strip {
  display: flex;
  gap: 9px;

  overflow-x: auto;

  padding: 8px 16px 17px;

  scrollbar-width: thin;
}

.thumbnail-button {
  position: relative;

  width: 92px;
  height: 65px;

  flex: 0 0 auto;
  overflow: hidden;

  padding: 0;
  border: 0;
  border-radius: 9px;

  background: #e8edf3;
  cursor: pointer;
}

.thumbnail-button img {
  width: 100%;
  height: 100%;

  display: block;
  object-fit: cover;

  transition: transform 0.35s ease;
}

.thumbnail-button:hover img {
  transform: scale(1.08);
}

.thumbnail-button span {
  position: absolute;
  right: 6px;
  bottom: 6px;

  width: 21px;
  height: 21px;

  display: grid;
  place-items: center;

  border-radius: 50%;

  background: rgba(4, 18, 39, 0.72);
  color: #ffffff;

  font-size: 9px;
}

/* Empty */

.empty-gallery {
  min-height: 480px;

  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;

  padding: 30px;
  text-align: center;
}

.empty-icon {
  width: 70px;
  height: 70px;

  display: grid;
  place-items: center;

  margin-bottom: 18px;

  border-radius: 50%;

  background: #edf5fd;
  color: #2476c8;

  font-size: 30px;
}

.empty-gallery h3 {
  margin: 0;

  color: #163e70;
  font-size: 20px;
}

.empty-gallery p {
  margin: 8px 0 0;

  color: #7a8798;
  font-size: 13px;
}

/* Full preview */

.preview-overlay {
  position: fixed;
  inset: 0;
  z-index: 10000;

  display: flex;
  align-items: center;
  justify-content: center;

  padding: 75px 90px 110px;

  background:
    radial-gradient(
      circle at center,
      rgba(29, 49, 76, 0.55),
      rgba(2, 7, 14, 0.98) 70%
    );
}

.preview-header {
  position: absolute;
  top: 0;
  left: 0;
  right: 0;

  display: flex;
  align-items: center;
  justify-content: space-between;

  padding: 18px 24px;

  color: #ffffff;
}

.preview-header div {
  display: flex;
  flex-direction: column;
}

.preview-header span {
  color: rgba(255, 255, 255, 0.55);
  font-size: 9px;
  letter-spacing: 1.3px;
  text-transform: uppercase;
}

.preview-header strong {
  margin-top: 4px;

  font-size: 14px;
  font-weight: 500;
}

.preview-close {
  width: 42px;
  height: 42px;

  display: inline-flex;
  align-items: center;
  justify-content: center;

  padding: 0;
  border: 0;
  border-radius: 50%;

  background: rgba(255, 255, 255, 0.13);
  color: #ffffff;

  font-size: 20px;
  cursor: pointer;

  transition: background 0.2s ease;
}

.preview-close:hover {
  background: rgba(255, 255, 255, 0.25);
}

.preview-image-container {
  position: relative;

  max-width: 88vw;
  max-height: 76vh;

  display: flex;
  align-items: center;
  justify-content: center;
}

.preview-image-container img {
  max-width: 100%;
  max-height: 76vh;

  display: block;
  object-fit: contain;

  border-radius: 10px;

  box-shadow: 0 25px 80px rgba(0, 0, 0, 0.5);
}

.preview-counter {
  position: absolute;
  left: 50%;
  bottom: 15px;

  padding: 7px 13px;

  border-radius: 30px;

  background: rgba(1, 7, 15, 0.7);
  color: #ffffff;

  font-size: 11px;
  transform: translateX(-50%);
}

.preview-arrow {
  position: absolute;
  top: 50%;

  width: 50px;
  height: 50px;

  display: inline-flex;
  align-items: center;
  justify-content: center;

  padding: 0;
  border: 1px solid rgba(255, 255, 255, 0.15);
  border-radius: 50%;

  background: rgba(255, 255, 255, 0.1);
  color: #ffffff;

  font-size: 22px;
  cursor: pointer;

  transform: translateY(-50%);
  transition:
    background 0.2s ease,
    transform 0.2s ease;
}

.preview-arrow:hover {
  background: rgba(255, 255, 255, 0.22);
  transform: translateY(-50%) scale(1.05);
}

.preview-arrow-left {
  left: 24px;
}

.preview-arrow-right {
  right: 24px;
}

.preview-thumbnails {
  position: absolute;
  left: 50%;
  bottom: 20px;

  max-width: 80vw;

  display: flex;
  gap: 8px;

  overflow-x: auto;

  padding: 7px;

  border: 1px solid rgba(255, 255, 255, 0.1);
  border-radius: 13px;

  background: rgba(5, 12, 22, 0.62);
  backdrop-filter: blur(10px);

  transform: translateX(-50%);
}

.preview-thumbnail {
  width: 62px;
  height: 45px;

  flex: 0 0 auto;
  overflow: hidden;

  padding: 0;
  border: 2px solid transparent;
  border-radius: 7px;

  background: #202a37;
  opacity: 0.55;
  cursor: pointer;

  transition:
    opacity 0.2s ease,
    border-color 0.2s ease;
}

.preview-thumbnail.active {
  border-color: #ffffff;
  opacity: 1;
}

.preview-thumbnail img {
  width: 100%;
  height: 100%;

  display: block;
  object-fit: cover;
}

/* Animations */

.gallery-modal-enter-active,
.gallery-modal-leave-active {
  transition: opacity 0.28s ease;
}

.gallery-modal-enter-active .gallery-popup,
.gallery-modal-leave-active .gallery-popup {
  transition:
    opacity 0.28s ease,
    transform 0.28s cubic-bezier(0.2, 0.75, 0.25, 1);
}

.gallery-modal-enter-from,
.gallery-modal-leave-to {
  opacity: 0;
}

.gallery-modal-enter-from .gallery-popup,
.gallery-modal-leave-to .gallery-popup {
  opacity: 0;
  transform: translateY(22px) scale(0.97);
}

.preview-modal-enter-active,
.preview-modal-leave-active {
  transition: opacity 0.22s ease;
}

.preview-modal-enter-from,
.preview-modal-leave-to {
  opacity: 0;
}

.image-change-enter-active,
.image-change-leave-active {
  transition:
    opacity 0.18s ease,
    transform 0.18s ease;
}

.image-change-enter-from {
  opacity: 0;
  transform: scale(0.985);
}

.image-change-leave-to {
  opacity: 0;
  transform: scale(1.015);
}

/* Tablet */

@media (max-width: 900px) {
  .gallery-popup {
    width: 95vw;
  }

  .gallery-main-image {
    min-height: 380px;
  }

  .preview-overlay {
    padding: 75px 70px 110px;
  }
}

/* Mobile */

@media (max-width: 640px) {
  .gallery-overlay {
    align-items: flex-end;
    padding: 0;
  }

  .gallery-popup {
    width: 100%;
    max-height: 94vh;

    border-radius: 22px 22px 0 0;
  }

  .gallery-header {
    padding: 18px 18px 15px;
  }

  .gallery-heading h2 {
    font-size: 20px;
  }

  .gallery-heading p {
    display: none;
  }

  .gallery-close {
    width: 38px;
    height: 38px;
  }

  .gallery-layout {
    grid-template-columns: 1fr;
    padding: 12px 12px 7px;
  }

  .gallery-main-image {
    min-height: 310px;
    border-radius: 14px;
  }

  .gallery-side {
    grid-template-columns: repeat(2, minmax(0, 1fr));
    grid-template-rows: none;
  }

  .gallery-side-image {
    min-height: 130px;
    border-radius: 10px !important;
  }

  .main-image-action {
    left: 14px;
    bottom: 14px;
  }

  .thumbnail-strip {
    padding: 6px 12px 14px;
  }

  .thumbnail-button {
    width: 76px;
    height: 55px;
  }

  .preview-overlay {
    padding: 70px 14px 105px;
  }

  .preview-image-container img {
    max-height: 70vh;
  }

  .preview-arrow {
    top: auto;
    bottom: 22px;

    width: 45px;
    height: 45px;

    transform: none;
  }

  .preview-arrow:hover {
    transform: scale(1.05);
  }

  .preview-arrow-left {
    left: calc(50% - 58px);
  }

  .preview-arrow-right {
    right: calc(50% - 58px);
  }

  .preview-thumbnails {
    display: none;
  }
}
</style>