<template>
  <Teleport to="body">
    <Transition name="modal-fade">
      <div
        v-if="show"
        class="modal-overlay"
        @click="$emit('close')"
      >
        <div
          class="modal"
          role="dialog"
          aria-modal="true"
          aria-label="Tree of Life Nature Resort Gallery"
          @click.stop
        >
          <!-- Header -->
          <div class="modal-header">
            <h2>Tree of Life Nature Resort - Gallery</h2>

            <button
              type="button"
              class="close-btn"
              aria-label="Close gallery"
              @click="$emit('close')"
            >
              <i class="fa-solid fa-xmark"></i>
            </button>
          </div>

          <!-- Scrollable body -->
          <div class="modal-body">
            <!-- Sticky category filters -->
            <div class="filter-bar">
              <button
                v-for="category in visibleCategories"
                :key="category"
                type="button"
                class="filter-btn"
                :class="{ active: selectedCategory === category }"
                @click="selectedCategory = category"
              >
                {{ category }}
              </button>
            </div>

            <!-- Gallery -->
            <div
              v-if="filteredPhotos.length"
              class="photos-grid"
            >
              <button
                v-for="(photo, index) in filteredPhotos"
                :key="`${photo.src}-${index}`"
                type="button"
                class="photo-card"
                @click="openPreview(index)"
              >
                <img
                  :src="photo.src"
                  :alt="photo.alt || 'Tree of Life Resort photo'"
                />
              </button>
            </div>

            <div
              v-else
              class="empty-gallery"
            >
              <i class="fa-regular fa-images"></i>
              <p>No photos available in this category.</p>
            </div>
          </div>
        </div>

        <!-- Full image preview -->
        <Transition name="preview-fade">
          <div
            v-if="previewPhoto"
            class="preview-overlay"
            @click.self="closePreview"
          >
            <button
              type="button"
              class="preview-close"
              aria-label="Close image preview"
              @click="closePreview"
            >
              <i class="fa-solid fa-xmark"></i>
            </button>

            <button
              v-if="filteredPhotos.length > 1"
              type="button"
              class="preview-arrow preview-left"
              aria-label="Previous image"
              @click="previousPhoto"
            >
              <i class="fa-solid fa-chevron-left"></i>
            </button>

            <div class="preview-content">
              <img
                :src="previewPhoto.src"
                :alt="previewPhoto.alt || 'Tree of Life Resort photo'"
              />

              <p v-if="previewPhoto.alt">
                {{ previewPhoto.alt }}
              </p>
            </div>

            <button
              v-if="filteredPhotos.length > 1"
              type="button"
              class="preview-arrow preview-right"
              aria-label="Next image"
              @click="nextPhoto"
            >
              <i class="fa-solid fa-chevron-right"></i>
            </button>
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
  watch,
} from "vue";

const props = defineProps({
  show: {
    type: Boolean,
    default: false,
  },

  photos: {
    type: Array,
    default: () => [],
  },

  categories: {
    type: Array,
    default: () => [
      "ALL",
      "FEATURED",
      "HOMES BUNGALOW",
      "FOOD & DINING",
      "PROPERTY",
      "STAFF",
    ],
  },
});

defineEmits(["close"]);

const selectedCategory = ref("ALL");
const previewIndex = ref(null);

function normalizeCategory(category) {
  return String(category || "FEATURED")
    .trim()
    .toUpperCase();
}

const visibleCategories = computed(() => {
  const categories = props.categories.map(normalizeCategory);

  const photoCategories = props.photos.map((photo) =>
    normalizeCategory(photo.category)
  );

  return [
    "ALL",
    ...new Set(
      [...categories, ...photoCategories].filter(
        (category) => category !== "ALL"
      )
    ),
  ];
});

const filteredPhotos = computed(() => {
  if (selectedCategory.value === "ALL") {
    return props.photos;
  }

  return props.photos.filter(
    (photo) =>
      normalizeCategory(photo.category) ===
      selectedCategory.value
  );
});

const previewPhoto = computed(() => {
  if (previewIndex.value === null) {
    return null;
  }

  return filteredPhotos.value[previewIndex.value] || null;
});

function openPreview(index) {
  previewIndex.value = index;
}

function closePreview() {
  previewIndex.value = null;
}

function nextPhoto() {
  if (!filteredPhotos.value.length) return;

  previewIndex.value =
    (previewIndex.value + 1) %
    filteredPhotos.value.length;
}

function previousPhoto() {
  if (!filteredPhotos.value.length) return;

  previewIndex.value =
    (
      previewIndex.value -
      1 +
      filteredPhotos.value.length
    ) % filteredPhotos.value.length;
}

function handleKeydown(event) {
  if (!props.show) return;

  if (event.key === "Escape") {
    if (previewPhoto.value) {
      closePreview();
    }
  }

  if (previewPhoto.value && event.key === "ArrowRight") {
    nextPhoto();
  }

  if (previewPhoto.value && event.key === "ArrowLeft") {
    previousPhoto();
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

      selectedCategory.value = "ALL";
      closePreview();
    }
  }
);

watch(selectedCategory, () => {
  closePreview();
});

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

/* Same popup structure as ReviewsModal */

.modal-overlay {
  position: fixed;
  inset: 0;
  z-index: 9999;

  display: flex;
  align-items: center;
  justify-content: center;

  padding: max(20px, 1.3889vw);

  background: rgba(7, 18, 34, 0.6);
  backdrop-filter: blur(max(4px, 0.2778vw));
}

.modal {
  width: 90%;
  max-width: max(1300px, 90.2778vw);
  height: 85vh;

  display: flex;
  flex-direction: column;
  overflow: hidden;

  background: #ffffff;
  border-radius: max(18px, 1.25vw);

  box-shadow:
    0 max(30px, 2.0833vw)
    max(90px, 6.25vw)
    rgba(0, 0, 0, 0.3);
}

/* Header */

.modal-header {
  position: relative;

  display: flex;
  align-items: center;
  justify-content: center;
  flex-shrink: 0;

  padding:
    max(22px, 1.5278vw)
    max(28px, 1.9444vw)
    max(18px, 1.25vw);

  border-bottom:
    max(1px, 0.0694vw)
    solid #edf0f4;
}

.modal-header h2 {
  margin: 0;

  color: #1a51ad;
  font-size: max(18px, 1.25vw);
  font-weight: 600;
}

.close-btn {
  position: absolute;
  right: max(22px, 1.5278vw);

  width: max(34px, 2.3611vw);
  height: max(34px, 2.3611vw);

  display: inline-flex;
  align-items: center;
  justify-content: center;

  border:
    max(1px, 0.0694vw)
    solid #e8edf3;
  border-radius: 50%;

  background: #ffffff;
  color: #6b7280;

  font-size: max(13px, 0.9028vw);
  cursor: pointer;

  transition:
    background 0.15s ease,
    color 0.15s ease;
}

.close-btn:hover {
  background: #1a51ad;
  color: #ffffff;
}

/* Scrollable body */

.modal-body {
  flex: 1;
  min-height: 0;

  overflow-y: auto;

  padding:
    0
    max(18px, 1.25vw)
    max(22px, 1.5278vw);
}

/* Filters */

.filter-bar {
  position: sticky;
  top: 0;
  z-index: 5;

  display: flex;
  flex-wrap: wrap;
  gap: max(9px, 0.625vw);

  padding:
    max(18px, 1.25vw)
    0
    max(16px, 1.1111vw);

  margin-bottom: max(14px, 0.9722vw);

  background: #ffffff;
  border-bottom:
    max(1px, 0.0694vw)
    solid #edf0f4;

  box-shadow:
    0 max(8px, 0.5556vw)
    max(12px, 0.8333vw)
    -8px rgba(28, 55, 89, 0.08);
}

.filter-btn {
  min-height: max(38px, 2.6389vw);

  padding:
    0
    max(15px, 1.0417vw);

  border:
    max(1px, 0.0694vw)
    solid #dfe3e8;

  border-radius: max(10px, 1vw);

  background: #fafbfc;
  color: #222222;

  font-size: max(12px, 0.8333vw);
  font-weight: 600;

  cursor: pointer;

  transition:
    background 0.2s ease,
    border-color 0.2s ease,
    color 0.2s ease;
}

.filter-btn:hover {
  border-color: #1a51ad;
  color: #1a51ad;
}

.filter-btn.active {
  border-color: #8fbbfe;
  background: #021c44;
  color: #ffffff;
}

/* Gallery layout */

.photos-grid {
  display: grid;
  grid-template-columns: repeat(3, minmax(0, 1fr));

  gap: max(12px, 0.8333vw);
}

.photo-card {
  position: relative;

  height: max(245px, 17.0139vw);

  overflow: hidden;
  padding: 0;

  border: none;
  border-radius: max(8px, 0.5556vw);

  background: #eef1f4;
  cursor: pointer;
}

.photo-card img {
  width: 100%;
  height: 100%;

  display: block;
  object-fit: cover;

  transition: transform 0.4s ease;
}

.photo-card::after {
  content: "";

  position: absolute;
  inset: 0;

  background: rgba(0, 0, 0, 0);

  transition: background 0.3s ease;
}

.photo-card:hover img {
  transform: scale(1.05);
}

.photo-card:hover::after {
  background: rgba(0, 0, 0, 0.08);
}

/* Empty state */

.empty-gallery {
  min-height: max(350px, 24.3056vw);

  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;

  color: #7b8490;
  text-align: center;
}

.empty-gallery i {
  margin-bottom: max(12px, 0.8333vw);
  font-size: max(36px, 2.5vw);
}

.empty-gallery p {
  margin: 0;
  font-size: max(14px, 0.9722vw);
}

/* Large photo preview */

.preview-overlay {
  position: fixed;
  inset: 0;
  z-index: 10000;

  display: flex;
  align-items: center;
  justify-content: center;

  padding:
    max(55px, 3.8194vw)
    max(80px, 5.5556vw);

  background: rgba(5, 8, 13, 0.96);
}

.preview-content {
  max-width: 90vw;
  max-height: 88vh;

  display: flex;
  flex-direction: column;
  align-items: center;
}

.preview-content img {
  max-width: 100%;
  max-height: 80vh;

  display: block;
  object-fit: contain;

  border-radius: max(7px, 0.4861vw);
}

.preview-content p {
  margin:
    max(12px, 0.8333vw)
    0
    0;

  color: rgba(255, 255, 255, 0.82);
  font-size: max(13px, 0.9028vw);
}

.preview-close,
.preview-arrow {
  display: inline-flex;
  align-items: center;
  justify-content: center;

  border: none;
  border-radius: 50%;

  background: rgba(255, 255, 255, 0.13);
  color: #ffffff;

  cursor: pointer;

  transition: background 0.2s ease;
}

.preview-close:hover,
.preview-arrow:hover {
  background: rgba(255, 255, 255, 0.28);
}

.preview-close {
  position: absolute;
  top: max(20px, 1.3889vw);
  right: max(22px, 1.5278vw);

  width: max(42px, 2.9167vw);
  height: max(42px, 2.9167vw);

  font-size: max(17px, 1.1806vw);
}

.preview-arrow {
  position: absolute;
  top: 50%;

  width: max(48px, 3.3333vw);
  height: max(48px, 3.3333vw);

  font-size: max(17px, 1.1806vw);

  transform: translateY(-50%);
}

.preview-left {
  left: max(20px, 1.3889vw);
}

.preview-right {
  right: max(20px, 1.3889vw);
}

/* Transitions */

.modal-fade-enter-active,
.modal-fade-leave-active {
  transition: opacity 0.25s ease;
}

.modal-fade-enter-from,
.modal-fade-leave-to {
  opacity: 0;
}

.preview-fade-enter-active,
.preview-fade-leave-active {
  transition: opacity 0.2s ease;
}

.preview-fade-enter-from,
.preview-fade-leave-to {
  opacity: 0;
}

/* Tablet */

@media (max-width: 992px) {
  .modal {
    width: 94%;
    height: 88vh;
  }

  .photos-grid {
    grid-template-columns: repeat(2, minmax(0, 1fr));
  }

  .photo-card {
    height: 250px;
  }
}

/* Mobile */

@media (max-width: 640px) {
  .modal-overlay {
    padding: 12px;
  }

  .modal {
    width: 100%;
    height: 92vh;
    border-radius: 16px;
  }

  .modal-header {
    justify-content: flex-start;
    padding: 18px 55px 16px 18px;
  }

  .modal-header h2 {
    font-size: 15px;
  }

  .close-btn {
    right: 14px;
  }

  .modal-body {
    padding: 0 12px 16px;
  }

  .filter-bar {
    flex-wrap: nowrap;
    overflow-x: auto;

    padding: 14px 0;

    scrollbar-width: none;
  }

  .filter-bar::-webkit-scrollbar {
    display: none;
  }

  .filter-btn {
    flex-shrink: 0;
    min-height: 36px;
    font-size: 11px;
  }

  .photos-grid {
    grid-template-columns: 1fr;
    gap: 10px;
  }

  .photo-card {
    height: 245px;
  }

  .preview-overlay {
    padding: 60px 14px 85px;
  }

  .preview-arrow {
    top: auto;
    bottom: 20px;
    transform: none;
  }

  .preview-left {
    left: calc(50% - 60px);
  }

  .preview-right {
    right: calc(50% - 60px);
  }
}
</style>