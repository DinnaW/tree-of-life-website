<template>
  <Teleport to="body">
    <Transition name="room-modal">
      <div
        v-if="isOpen && room"
        class="room-modal-overlay"
        @click.self="closeModal"
      >
        <div
          class="room-modal-container"
          role="dialog"
          aria-modal="true"
          :aria-labelledby="`room-title-${room.id}`"
        >
          <!-- CLOSE ROOM POPUP -->
          <button
            type="button"
            class="room-modal-close"
            aria-label="Close room details"
            @click="closeModal"
          >
            <svg
              viewBox="0 0 24 24"
              fill="none"
              stroke="currentColor"
            >
              <path d="M6 6l12 12" />
              <path d="M18 6 6 18" />
            </svg>
          </button>

          <!-- ROOM GALLERY -->
          <div class="room-modal-images">
            <div class="room-gallery-grid">
              <!-- LARGE SELECTED IMAGE -->
              <button
                type="button"
                class="room-gallery-item room-gallery-main"
                aria-label="Open selected room image"
                @click="openFullGallery(activeImageIndex)"
              >
                <Transition name="gallery-switch" mode="out-in">
                  <img
                    :key="activeImage"
                    :src="activeImage"
                    :alt="`${room.name} image ${activeImageIndex + 1}`"
                    @error="handleImageError"
                  />
                </Transition>

                <span class="main-image-count">
                  {{ activeImageIndex + 1 }}
                  /
                  {{ roomImages.length }}
                </span>
              </button>

              <!-- SMALL IMAGE GRID -->
              <div class="room-gallery-side">
                <button
                  v-for="galleryItem in previewGalleryImages"
                  :key="`${room.id}-preview-${galleryItem.index}`"
                  type="button"
                  class="room-gallery-item room-gallery-small"
                  :class="{
                    active: activeImageIndex === galleryItem.index
                  }"
                  :aria-label="`Select room image ${galleryItem.index + 1}`"
                  @click="selectGalleryImage(galleryItem.index)"
                >
                  <img
                    :src="galleryItem.image"
                    :alt="`${room.name} image ${galleryItem.index + 1}`"
                    @error="handleImageError"
                  />

                  <span class="room-gallery-hover"></span>
                </button>
              </div>

              <!-- SHOW ALL PHOTOS -->
              <button
                v-if="roomImages.length > 1"
                type="button"
                class="show-all-photos"
                @click.stop="openFullGallery(activeImageIndex)"
              >
                <i class="fa-regular fa-images"></i>
                Show all photos
              </button>
            </div>
          </div>

          <!-- ROOM CONTENT -->
          <div class="room-modal-content">
            <div class="room-modal-details">
              <p
                v-if="room.subtitle"
                class="room-modal-eyebrow"
              >
                {{ room.subtitle }}
              </p>

              <div class="room-modal-heading-row">
                <h2 :id="`room-title-${room.id}`">
                  {{ room.name }}
                </h2>

                <span v-if="room.mostBooked" class="modal-badge modal-badge-booked">
                  MOST BOOKED
                </span>

                <span v-if="room.roomsLeft" class="modal-badge modal-badge-scarcity">
                  Only {{ room.roomsLeft }} left
                </span>
              </div>

              <!-- BED & MEAL PLAN (kept in sync with the room card) -->
              <div class="room-modal-plans">
                <div class="option">
                  <label>Bed plan</label>

                  <div class="pill-group" role="group" aria-label="Bed plan">
                    <button
                      v-for="bed in ['Single', 'Double', 'Triple']"
                      :key="bed"
                      type="button"
                      class="pill"
                      :class="{ active: room.bed === bed }"
                      @click="room.bed = bed"
                    >
                      {{ bed }}
                    </button>
                  </div>
                </div>

                <div class="option">
                  <label>Meal plan</label>

                  <div class="pill-group" role="group" aria-label="Meal plan">
                    <button
                      v-for="meal in ['Bed & Breakfast', 'Half Board']"
                      :key="meal"
                      type="button"
                      class="pill"
                      :class="{ active: room.meal === meal }"
                      @click="room.meal = meal"
                    >
                      {{ meal }}
                    </button>
                  </div>
                </div>
              </div>

              <!-- ROOM INFORMATION -->
              <div class="room-information">
                <div class="room-information-item">
                  <span>Guests</span>
                  <strong>
                    Up to {{ room.guests || 2 }} guests
                  </strong>
                </div>

                <div class="room-information-item">
                  <span>Room size</span>
                  <strong>{{ room.size || "38 m²" }}</strong>
                </div>

                <div class="room-information-item">
                  <span>View</span>
                  <strong>
                    {{ room.view || room.tag || "Nature view" }}
                  </strong>
                </div>
              </div>

              <!-- ABOUT -->
              <section class="room-detail-section">
                <h3>About this room</h3>

                <p>
                  {{
                    room.description ||
                    "Enjoy a comfortable and relaxing stay in this thoughtfully designed room, surrounded by the peaceful natural beauty of the resort."
                  }}
                </p>
              </section>

              <!-- FACILITIES -->
              <section
                v-if="normalisedFacilities.length"
                class="room-detail-section"
              >
                <h3>Room facilities</h3>

                <div class="facilities-grid">
                  <div
                    v-for="facility in normalisedFacilities"
                    :key="facility.name"
                    class="facility-item"
                  >
                    <span class="facility-icon">
                      <i :class="facility.icon"></i>
                    </span>

                    <span>{{ facility.name }}</span>
                  </div>
                </div>
              </section>

              <!-- BATHROOM FACILITIES -->
              <section
                v-if="bathroomFacilities.length"
                class="room-detail-section"
              >
                <h3>Bathroom facilities</h3>

                <div class="facilities-grid">
                  <div
                    v-for="facility in bathroomFacilities"
                    :key="facility"
                    class="facility-item"
                  >
                    <span class="facility-icon">
                      <i :class="facilityIcon(facility)"></i>
                    </span>

                    <span>{{ facility }}</span>
                  </div>
                </div>
              </section>

              <!-- INCLUDED ITEMS -->
              <section
                v-if="includedItems.length"
                class="room-detail-section"
              >
                <h3>Included with your stay</h3>

                <div class="facilities-grid">
                  <div
                    v-for="item in includedItems"
                    :key="item"
                    class="facility-item"
                  >
                    <span class="facility-icon">
                      <i :class="facilityIcon(item)"></i>
                    </span>

                    <span>{{ item }}</span>
                  </div>
                </div>
              </section>

              <!-- ROOM POLICIES -->
              <section class="room-detail-section">
                <h3>Room policies</h3>

                <div class="room-policies">
                  <div>
                    <span>Check-in</span>
                    <strong>
                      {{ room.checkIn || "From 2:00 PM" }}
                    </strong>
                  </div>

                  <div>
                    <span>Check-out</span>
                    <strong>
                      {{ room.checkOut || "Until 11:00 AM" }}
                    </strong>
                  </div>

                  <div>
                    <span>Smoking</span>
                    <strong>
                      {{ room.smoking || "Non-smoking room" }}
                    </strong>
                  </div>
                </div>
              </section>

              <!-- CANCELLATION POLICY -->
              <section class="room-detail-section">
                <h3>Cancellation policy</h3>

                <div
                  class="cancellation-callout"
                  :class="{ 'non-refundable': isNonRefundable }"
                >
                  <i :class="isNonRefundable ? 'fa-solid fa-triangle-exclamation' : 'fa-solid fa-circle-check'"></i>
                  <span>
                    {{ room.cancellation || "Free cancellation up to 48 hours before check-in" }}
                  </span>
                </div>
              </section>
            </div>

            <!-- BOOKING CARD -->
            <aside class="room-booking-card">
              <span class="booking-label">
                Starting from
              </span>

              <span
                v-if="room.offer"
                class="booking-offer"
              >
                <i class="fa-solid fa-tag"></i>
                {{ room.offer }}
              </span>

              <div class="booking-price">
                <div class="booking-price-values">
                  <span
                    v-if="room.originalPrice"
                    class="booking-original-price"
                  >
                    $ {{ formatPrice(room.originalPrice) }}
                  </span>

                  <strong>
                    $ {{ formatPrice(room.price) }}
                  </strong>
                </div>

                <span>/ night</span>
              </div>

              <div class="price-breakdown-list">
                <div class="price-breakdown-row">
                  <span>
                    $ {{ formatPrice(room.price) }} × {{ nights }} {{ nights === 1 ? "night" : "nights" }}
                    × {{ room.roomCount || 1 }} {{ (room.roomCount || 1) > 1 ? "rooms" : "room" }}
                  </span>
                  <span>$ {{ formatPrice(room.price * (room.roomCount || 1) * nights) }}</span>
                </div>

                <div v-if="room.extraBeds" class="price-breakdown-row">
                  <span>Extra beds ({{ room.extraBeds }} × $ {{ extraBedFee }})</span>
                  <span>$ {{ formatPrice(room.extraBeds * extraBedFee) }}</span>
                </div>

                <div class="price-breakdown-row price-breakdown-total">
                  <span>Total</span>
                  <span>$ {{ formatPrice(roomTotal) }}</span>
                </div>
              </div>

              <div class="booking-benefits">
                <p>✓ Breakfast included</p>
                <p>✓ No booking fees</p>
                <p>✓ Free parking</p>
              </div>

              <button
                type="button"
                class="room-booking-button"
                @click="reserveRoom"
              >
                Reserve this room
              </button>


            </aside>
          </div>
        </div>
      </div>
    </Transition>
  </Teleport>

  <!-- FULL-SCREEN GALLERY -->
  <Teleport to="body">
    <Transition name="photo-lightbox">
      <div
        v-if="isGalleryOpen && room"
        class="photo-lightbox-overlay"
        role="dialog"
        aria-modal="true"
        aria-label="Room photo gallery"
        @click.self="closeFullGallery"
      >
        <div class="photo-lightbox-container">
          <!-- CLOSE FULL GALLERY -->
          <button
            type="button"
            class="photo-lightbox-close"
            aria-label="Close photo gallery"
            @click="closeFullGallery"
          >
            <svg
              viewBox="0 0 24 24"
              fill="none"
              stroke="currentColor"
            >
              <path d="M6 6l12 12" />
              <path d="M18 6 6 18" />
            </svg>
          </button>

          <!-- IMAGE COUNTER -->
          <span class="photo-lightbox-count">
            {{ lightboxImageIndex + 1 }}
            /
            {{ roomImages.length }}
          </span>

          <!-- PREVIOUS -->
          <button
            v-if="roomImages.length > 1"
            type="button"
            class="photo-lightbox-arrow photo-lightbox-arrow-left"
            aria-label="Previous image"
            @click="showPreviousLightboxImage"
          >
            <i class="fa-solid fa-chevron-left"></i>
          </button>

          <!-- FULL IMAGE -->
          <div class="photo-lightbox-image">
            <Transition name="gallery-switch" mode="out-in">
              <img
                :key="lightboxActiveImage"
                :src="lightboxActiveImage"
                :alt="`${room.name} gallery image ${lightboxImageIndex + 1}`"
                @error="handleImageError"
              />
            </Transition>
          </div>

          <!-- NEXT -->
          <button
            v-if="roomImages.length > 1"
            type="button"
            class="photo-lightbox-arrow photo-lightbox-arrow-right"
            aria-label="Next image"
            @click="showNextLightboxImage"
          >
            <i class="fa-solid fa-chevron-right"></i>
          </button>

          <!-- FULL GALLERY THUMBNAILS -->
          <div
            v-if="roomImages.length > 1"
            class="photo-lightbox-thumbnails"
          >
            <button
              v-for="(image, index) in roomImages"
              :key="`${room.id}-lightbox-${index}`"
              type="button"
              class="photo-lightbox-thumbnail"
              :class="{
                active: lightboxImageIndex === index
              }"
              :aria-label="`Show image ${index + 1}`"
              @click="selectLightboxImage(index)"
            >
              <img
                :src="image"
                :alt="`${room.name} thumbnail ${index + 1}`"
                @error="handleImageError"
              />
            </button>
          </div>
        </div>
      </div>
    </Transition>
  </Teleport>
</template>

<script setup>
import {
  computed,
  onBeforeUnmount,
  onMounted,
  ref,
  watch
} from "vue";

const props = defineProps({
  isOpen: {
    type: Boolean,
    default: false
  },

  room: {
    type: Object,
    default: null
  },

  nights: {
    type: Number,
    default: 1
  },

  extraBedFee: {
    type: Number,
    default: 15
  }
});

const emit = defineEmits([
  "close",
  "reserve"
]);

const activeImageIndex = ref(0);
const lightboxImageIndex = ref(0);
const isGalleryOpen = ref(false);

const fallbackImage =
  `${import.meta.env.BASE_URL}images/room1.jpg`;

/* ALL ROOM IMAGES */
const roomImages = computed(() => {
  if (!props.room) {
    return [fallbackImage];
  }

  if (
    Array.isArray(props.room.images) &&
    props.room.images.length
  ) {
    const validImages =
      props.room.images.filter(Boolean);

    if (validImages.length) {
      return validImages;
    }
  }

  if (props.room.image) {
    return [props.room.image];
  }

  return [fallbackImage];
});

const activeImage = computed(() => {
  return (
    roomImages.value[activeImageIndex.value] ||
    roomImages.value[0] ||
    fallbackImage
  );
});

const previewGalleryImages = computed(() => {
  return roomImages.value
    .slice(0, 4)
    .map((image, index) => ({
      image,
      index
    }));
});

const lightboxActiveImage = computed(() => {
  return (
    roomImages.value[lightboxImageIndex.value] ||
    roomImages.value[0] ||
    fallbackImage
  );
});

const normalisedFacilities = computed(() => {
  const facilities = props.room?.facilities;

  if (!Array.isArray(facilities)) {
    return [];
  }

  return facilities.map((facility, index) => {
    if (typeof facility === "string") {
      return {
        name: facility,
        icon: "fa-solid fa-check"
      };
    }

    return {
      name:
        facility?.name ||
        `Facility ${index + 1}`,

      icon:
        facility?.icon ||
        "fa-solid fa-check"
    };
  });
});

const FACILITY_ICON_MAP = {
  "private bathroom": "fa-solid fa-bath",
  "hot water": "fa-solid fa-temperature-high",
  "walk-in shower": "fa-solid fa-shower",
  "rain shower": "fa-solid fa-shower",
  "hair dryer": "fa-solid fa-wind",
  "fresh towels": "fa-solid fa-soap",

  "daily breakfast": "fa-solid fa-mug-saucer",
  "free wifi": "fa-solid fa-wifi",
  "free parking": "fa-solid fa-square-parking",
  "tea and coffee facilities": "fa-solid fa-mug-hot",
  "room service": "fa-solid fa-bell-concierge"
};

function facilityIcon(name) {
  return FACILITY_ICON_MAP[name?.toLowerCase()] || "fa-solid fa-circle-check";
}

const bathroomFacilities = computed(() => {
  if (
    Array.isArray(props.room?.bathroomFacilities) &&
    props.room.bathroomFacilities.length
  ) {
    return props.room.bathroomFacilities;
  }

  return [
    "Private bathroom",
    "Hot water",
    "Walk-in shower",
    "Fresh towels"
  ];
});

const includedItems = computed(() => {
  if (
    Array.isArray(props.room?.included) &&
    props.room.included.length
  ) {
    return props.room.included;
  }

  return [
    "Daily breakfast",
    "Free WiFi",
    "Free parking",
    "Tea and coffee facilities"
  ];
});

const roomTotal = computed(() => {
  if (!props.room) return 0;

  const nightsCost = props.room.price * (props.room.roomCount || 1) * props.nights;
  const extraBedsCost = (props.room.extraBeds || 0) * props.extraBedFee;

  return nightsCost + extraBedsCost;
});

const isNonRefundable = computed(() => {
  return /non-refundable/i.test(props.room?.cancellation || "");
});

function selectGalleryImage(index) {
  activeImageIndex.value = index;
}

function openFullGallery(index = 0) {
  lightboxImageIndex.value = index;
  isGalleryOpen.value = true;
}

function selectLightboxImage(index) {
  lightboxImageIndex.value = index;
}

function closeFullGallery() {
  isGalleryOpen.value = false;
}

function showPreviousLightboxImage() {
  if (!roomImages.value.length) {
    return;
  }

  lightboxImageIndex.value =
    lightboxImageIndex.value === 0
      ? roomImages.value.length - 1
      : lightboxImageIndex.value - 1;
}

function showNextLightboxImage() {
  if (!roomImages.value.length) {
    return;
  }

  lightboxImageIndex.value =
    lightboxImageIndex.value ===
    roomImages.value.length - 1
      ? 0
      : lightboxImageIndex.value + 1;
}

function handleImageError(event) {
  if (!event?.target) {
    return;
  }

  const imageElement = event.target;

  if (imageElement.dataset.fallbackApplied === "true") {
    return;
  }

  imageElement.dataset.fallbackApplied = "true";
  imageElement.src = fallbackImage;
}

function closeModal() {
  isGalleryOpen.value = false;
  emit("close");
}

function reserveRoom() {
  emit("reserve", props.room);
}

function formatPrice(price) {
  return new Intl.NumberFormat("en-US").format(
    Number(price) || 0
  );
}

function handleKeydown(event) {
  if (
    event.key === "Escape" &&
    isGalleryOpen.value
  ) {
    closeFullGallery();
    return;
  }

  if (
    event.key === "Escape" &&
    props.isOpen
  ) {
    closeModal();
    return;
  }

  if (!isGalleryOpen.value) {
    return;
  }

  if (event.key === "ArrowLeft") {
    showPreviousLightboxImage();
  }

  if (event.key === "ArrowRight") {
    showNextLightboxImage();
  }
}

watch(
  () => [
    props.isOpen,
    props.room
  ],
  () => {

    activeImageIndex.value = 0;
    lightboxImageIndex.value = 0;
    isGalleryOpen.value = false;

    if (
      props.isOpen &&
      props.room
    ) {
      document.body.style.overflow = "hidden";
    } else {
      document.body.style.overflow = "";
    }
  },
  {
    immediate: true
  }
);

onMounted(() => {
  window.addEventListener(
    "keydown",
    handleKeydown
  );
});

onBeforeUnmount(() => {
  window.removeEventListener(
    "keydown",
    handleKeydown
  );

  document.body.style.overflow = "";
});
</script>

<style scoped>
* {
  box-sizing: border-box;
  font-family: "Figtree", sans-serif;
}

/* ROOM MODAL */

.room-modal-overlay {
  position: fixed;
  inset: 0;
  z-index: 5000;
  display: flex;
  align-items: center;
  justify-content: center;
  padding: clamp(24px, 2vw, 42px);
  background: rgba(7, 18, 34, 0.72);
  backdrop-filter: blur(5px);
}

.room-modal-container {
  position: relative;
  width: min(
    clamp(1180px, 82vw, 1380px),
    calc(100vw - clamp(48px, 4vw, 84px))
  );

  max-height: calc(
    100vh - clamp(48px, 4vw, 84px)
  );

  overflow-x: hidden;
  overflow-y: auto;
  background: #ffffff;
  border: 1px solid rgba(218, 226, 237, 0.9);
  border-radius: clamp(22px, 1.4vw, 28px);
  box-shadow:
    0 30px 90px
    rgba(0, 0, 0, 0.3);

  scrollbar-width: thin;
  scrollbar-color: #b8c4d2 transparent;
}

.room-modal-container::-webkit-scrollbar {
  width: 7px;
}

.room-modal-container::-webkit-scrollbar-thumb {
  background: #b8c4d2;
  border-radius: 10px;
}

/* CLOSE BUTTON */

.room-modal-close {
  position: absolute;
  top: clamp(18px, 1.1vw, 24px);
  right: clamp(18px, 1.1vw, 24px);
  z-index: 20;
  width: clamp(44px, 2.5vw, 52px);
  height: clamp(44px, 2.5vw, 52px);
  display: flex;
  align-items: center;
  justify-content: center;
  padding: 0;
  color: #1a51ad;
  background: rgba(255, 255, 255, 0.96);
  border: 1px solid rgba(218, 226, 237, 0.9);
  border-radius: 50%;
  cursor: pointer;
  box-shadow:
    0 5px 18px
    rgba(0, 0, 0, 0.18);
  transition:
    color 0.25s ease,
    background 0.25s ease,
    transform 0.25s ease;
}

.room-modal-close:hover {
  color: #ffffff;
  background: #1a51ad;
  transform: rotate(90deg);
}

.room-modal-close svg {
  width: clamp(21px, 1.2vw, 25px);
  height: clamp(21px, 1.2vw, 25px);
  stroke-width: 1.8;
  stroke-linecap: round;
}

/* GALLERY PREVIEW */

.room-modal-images {
  padding: clamp(10px, 0.8vw, 16px);
  background: #f3f6fa;
  border-radius:
    clamp(22px, 1.4vw, 28px)
    clamp(22px, 1.4vw, 28px)
    0
    0;
}

.room-gallery-grid {
  position: relative;
  display: grid;
  grid-template-columns:
    minmax(0, 1.3fr)
    minmax(360px, 1fr);

  gap: clamp(6px, 0.45vw, 10px);
  height: clamp(420px, 30vw, 540px);
}

.room-gallery-item {
  position: relative;
  width: 100%;
  height: 100%;
  padding: 0;
  overflow: hidden;
  background: #e5eaf0;
  border: 2px solid transparent;
  font-family: inherit;
  cursor: pointer;
}

.room-gallery-item img {
  width: 100%;
  height: 100%;
  display: block;
  object-fit: cover;
  transition: transform 0.4s ease;
}

.room-gallery-item:hover img {
  transform: scale(1.035);
}

/* LARGE IMAGE */

.room-gallery-main {
  border-radius:
    clamp(14px, 0.9vw, 18px)
    0
    0
    clamp(14px, 0.9vw, 18px);
}

.main-image-count {
  position: absolute;
  left: clamp(14px, 1vw, 20px);
  bottom: clamp(14px, 1vw, 20px);
  z-index: 4;
  min-height: 34px;
  display: inline-flex;
  align-items: center;
  justify-content: center;
  padding: 0 13px;
  color: #ffffff;
  background: rgba(13, 27, 46, 0.72);
  border: 1px solid rgba(255, 255, 255, 0.22);
  border-radius: 18px;
  backdrop-filter: blur(6px);
  font-size: clamp(11px, 0.7vw, 13px);
  font-weight: 600;
}

/* SMALL IMAGE GRID */

.room-gallery-side {
  display: grid;
  grid-template-columns:
    repeat(2, minmax(0, 1fr));
  grid-template-rows:
    repeat(2, minmax(0, 1fr));
  gap: clamp(6px, 0.45vw, 10px);
  min-width: 0;
  min-height: 0;
}

.room-gallery-small {
  min-width: 0;
  min-height: 0;
}

.room-gallery-hover {
  position: absolute;
  inset: 0;
  pointer-events: none;
  background: rgba(9, 22, 40, 0);
  transition: background 0.25s ease;
}

.room-gallery-small:hover .room-gallery-hover {
  background: rgba(9, 22, 40, 0.1);
}

/* SELECTED SMALL IMAGE */

.room-gallery-small.active {
  border-color: #1a51ad;
}

.room-gallery-small.active::before {
  content: "";

  position: absolute;
  inset: 0;
  z-index: 2;

  border: 2px solid #ffffff;

  pointer-events: none;
}

.room-gallery-small.active .room-gallery-hover {
  background: rgba(26, 81, 173, 0.08);
}

/* TOP-RIGHT IMAGE */

.room-gallery-small:nth-child(2) {
  border-radius:
    0
    clamp(14px, 0.9vw, 18px)
    0
    0;
}

/* BOTTOM-RIGHT IMAGE */

.room-gallery-small:nth-child(4) {
  border-radius:
    0
    0
    clamp(14px, 0.9vw, 18px)
    0;
}

.show-all-photos {
  position: absolute;
  right: clamp(14px, 1vw, 20px);
  bottom: clamp(14px, 1vw, 20px);
  z-index: 10;
  min-height: clamp(38px, 2.5vw, 46px);
  display: inline-flex;
  align-items: center;
  justify-content: center;
  gap: 8px;
  padding:
    0
    clamp(14px, 1vw, 20px);

  color: #ffffff;
  background: rgba(20, 29, 39, 0.86);
  border: 1px solid rgba(255, 255, 255, 0.25);
  border-radius: clamp(7px, 0.5vw, 10px);
  backdrop-filter: blur(6px);
  font-family: inherit;
  font-size: clamp(12px, 0.78vw, 15px);
  font-weight: 700;
  cursor: pointer;
  box-shadow:
    0 8px 24px
    rgba(0, 0, 0, 0.24);
  transition:
    background 0.25s ease,
    transform 0.25s ease;
}

.show-all-photos:hover {
  background: #1a51ad;
  transform: translateY(-2px);
}

/* MODAL CONTENT */

.room-modal-content {
  display: grid;

  grid-template-columns:
    minmax(0, 1fr)
    clamp(310px, 20vw, 360px);

  gap: clamp(38px, 2.3vw, 50px);
  padding: clamp(38px, 2.3vw, 52px);
}

.room-modal-details {
  min-width: 0;
}

.room-modal-eyebrow {
  margin: 0 0 8px;
  color: #2d6adc;
  font-size: clamp(12px, 0.72vw, 14px);
  font-weight: 700;
  letter-spacing: 1.2px;
  text-transform: uppercase;
}

.room-modal-details h2 {
  margin: 0;
  color: #1a51ad;
  font-size: clamp(30px, 2.4vw, 46px);
  font-weight: 600;
  line-height: 1.2;
}

/* HEADING ROW + BADGES */

.room-modal-heading-row {
  display: flex;
  margin-bottom: 45px;
  align-items: center;
  flex-wrap: wrap;
  gap: 12px;
}

.modal-badge {
  display: inline-flex;
  align-items: center;
  height: 26px;
  padding: 0 12px;
  border-radius: 4px;
  font-size: 11px;
  font-weight: 700;
  letter-spacing: 0.6px;
  text-transform: uppercase;
  white-space: nowrap;
}

.modal-badge-booked {
  background: #eef3fa;
  color: #1a51ad;
}

.modal-badge-scarcity {
  background: #fdeeee;
  color: #c62828;
}

/* BED / MEAL PLAN PILLS */

.room-modal-plans {
  display: flex;
  flex-wrap: wrap;
  gap: 24px;
  margin-top: clamp(20px, 1.3vw, 26px);
}

.room-modal-plans .option label {
  display: block;
  margin-bottom: 8px;
  color: #87909c;
  font-size: 12px;
  font-weight: 600;
  letter-spacing: 0.4px;
  text-transform: uppercase;
}

.pill-group {
  display: flex;
  gap: 8px;
  flex-wrap: wrap;
}

.pill {
  height: 36px;
  padding: 0 16px;
  border-radius: 9px;
  border: 1px solid #d5dbe3;
  background: #ffffff;
  color: #25364c;
  font-family: inherit;
  font-size: 12.5px;
  font-weight: 500;
  cursor: pointer;
  transition: background 0.15s ease, border-color 0.15s ease, color 0.15s ease;
}

.pill:hover {
  border-color: #1a51ad;
}

.pill.active {
  background: #1a51ad;
  border-color: #1a51ad;
  color: #ffffff;
}

/* CANCELLATION POLICY */

.cancellation-callout {
  display: flex;
  align-items: flex-start;
  gap: 12px;
  padding: 16px 18px;
  background: #edf8ee;
  border: 1px solid #bfe5cb;
  border-radius: 12px;
  color: #1b5e2e;
  font-size: clamp(13px, 0.8vw, 15px);
  line-height: 1.6;
}

.cancellation-callout i {
  margin-top: 2px;
  color: #1b7a3d;
}

.cancellation-callout.non-refundable {
  background: #fdf3ee;
  border-color: #f3d0bd;
  color: #8a3d13;
}

.cancellation-callout.non-refundable i {
  color: #c8631f;
}

/* PRICE BREAKDOWN */

.price-breakdown-list {
  padding-bottom: clamp(20px, 1.25vw, 26px);
  margin-bottom: clamp(20px, 1.25vw, 26px);
  border-bottom: 1px solid #e7ebf0;
}

.price-breakdown-row {
  display: flex;
  align-items: baseline;
  justify-content: space-between;
  gap: 10px;
  margin-top: 10px;
  color: #56616e;
  font-size: 12.5px;
}

.price-breakdown-row span:first-child {
  max-width: 65%;
}

.price-breakdown-total {
  margin-top: 14px;
  padding-top: 12px;
  border-top: 1px dashed #e7ebf0;
  color: #1a51ad;
  font-size: 16px;
  font-weight: 700;
}

/* ROOM INFORMATION */

.room-information {
  display: grid;
  grid-template-columns:
    repeat(2, minmax(0, 1fr));
  gap: clamp(14px, 0.9vw, 18px);
  margin:
    clamp(28px, 1.8vw, 36px)
    0;
}

.room-information-item {
  padding: clamp(15px, 0.95vw, 19px);
  background: #f6f8fb;
  border: 1px solid #edf0f4;
  border-radius: clamp(10px, 0.65vw, 14px);
}

.room-information-item span,
.room-policies span {
  display: block;
  color: #87909c;
  font-size: clamp(11px, 0.67vw, 13px);
}

.room-information-item strong,
.room-policies strong {
  display: block;
  margin-top: 3px;
  color: #25364c;
  font-size: clamp(13px, 0.78vw, 15px);
  font-weight: 600;
}

/* DETAIL SECTIONS */

.room-detail-section {
  padding: clamp(25px, 1.55vw, 32px) 0;
  border-top: 1px solid #e8edf2;
}

.room-detail-section h3 {
  margin: 0 0 clamp(15px, 0.95vw, 19px);
  color: #25364c;
  font-size: clamp(19px, 1.18vw, 24px);
  font-weight: 600;
}

.room-detail-section p {
  margin: 0;
  color: #626d7a;
  font-size: clamp(14px, 0.85vw, 17px);
  line-height: 1.8;
}

/* FACILITIES */

.facilities-grid {
  display: grid;
  grid-template-columns:
    repeat(5, minmax(0, 1fr));
  gap:
    clamp(12px, 0.8vw, 16px)
    clamp(20px, 1.3vw, 26px);
}

.facility-item,
.included-item {
  display: flex;
  align-items: center;
  gap: clamp(9px, 0.6vw, 12px);
  color: #56616e;
  font-size: clamp(13px, 0.78vw, 15px);
}

.facility-icon {
  width: clamp(30px, 1.8vw, 38px);
  height: clamp(30px, 1.8vw, 38px);
  display: inline-flex;
  align-items: center;
  justify-content: center;
  flex-shrink: 0;
  color: #1a51ad;
  background: #edf3fb;
  border-radius: clamp(8px, 0.5vw, 11px);
  font-size: clamp(12px, 0.72vw, 14px);
}

.facility-check {
  width: clamp(22px, 1.35vw, 27px);
  height: clamp(22px, 1.35vw, 27px);
  display: inline-flex;
  align-items: center;
  justify-content: center;
  flex-shrink: 0;
  color: #ffffff;
  background: #25a46c;
  border-radius: 50%;
  font-size: 10px;
}

.included-list {
  display: grid;
  gap: clamp(11px, 0.7vw, 14px);
}

.booking-price-values {
  display: flex;
  align-items: baseline;
  flex-wrap: wrap;
  gap: clamp(8px, 0.55vw, 12px);
}

.booking-price-values {
  display: flex;
  align-items: baseline;
  gap: clamp(8px, 0.55vw, 12px);
  flex-wrap: wrap;
}

.booking-price .booking-original-price {
  margin-left: 0;
  color: #e53935 !important;
  font-size: clamp(16px, 1vw, 20px);
  font-weight: 300;
  text-decoration: line-through;
  text-decoration-color: #e53935;
  text-decoration-thickness: 2px;
  opacity: 1;
}

.booking-price-values strong {
  color: #1a51ad;
}

/* POLICIES */

.room-policies {
  display: grid;
  grid-template-columns:
    repeat(3, minmax(0, 1fr));
  gap: clamp(12px, 0.8vw, 16px);
}

.room-policies > div {
  padding: clamp(14px, 0.9vw, 18px);
  background: #f6f8fb;
  border: 1px solid #edf0f4;
  border-radius: clamp(10px, 0.65vw, 14px);
}

/* BOOKING CARD */

.room-booking-card {
  position: sticky;
  top: clamp(20px, 1.25vw, 26px);
  align-self: start;
  padding: clamp(25px, 1.5vw, 34px);
  background: #ffffff;
  border: 1px solid #dfe5ed;
  border-radius: clamp(15px, 0.95vw, 19px);
  box-shadow:
    0 12px 35px
    rgba(28, 55, 89, 0.1);
}

.booking-label {
  color: #7c8693;
  font-size: clamp(12px, 0.72vw, 14px);
}

.booking-offer {
  width: fit-content;
  display: inline-flex;
  align-items: center;
  gap: 7px;
  margin-top: 10px;
  padding: 7px 11px;
  color: #1b7a3d;
  background: #edf8ee;
  border: max(1px, 0.0694vw) solid #bfe5cb;
  border-radius: 6px;
  font-size: clamp(11px, 0.68vw, 13px);
  font-weight: 700;
}

.booking-offer i {
  font-size: clamp(10px, 0.62vw, 12px);
}

.booking-price {
  padding-bottom: clamp(20px, 1.25vw, 26px);
  margin-top: 5px;
  margin-bottom: clamp(20px, 1.25vw, 26px);
  border-bottom: 1px solid #e7ebf0;
}

.booking-price strong {
  color: #1a51ad;
  font-size: clamp(25px, 1.55vw, 32px);
  font-weight: 700;
}

.booking-price > span {
  margin-left: 4px;
  color: #7c8693;
  font-size: clamp(11px, 0.67vw, 13px);
}

.booking-benefits {
  color: #56616e;
  font-size: clamp(12px, 0.72vw, 14px);
}

.booking-benefits p {
  margin: 0 0 10px;
}

.room-booking-button {
  width: 100%;
  min-height: clamp(46px, 2.8vw, 56px);
  margin-top: 12px;
  padding: 0 16px;
  color: #ffffff;
  background: #021c44;
  border: none;
  border-radius: clamp(9px, 0.6vw, 12px);
  font-family: inherit;
  font-size: clamp(13px, 0.78vw, 15px);
  font-weight: 700;
  cursor: pointer;
  transition:
    background 0.25s ease,
    transform 0.25s ease;
}

.room-booking-button:hover {
  background: #032d6b;
  transform: translateY(-1px);
}

.booking-message {
  margin: 12px 0 0;
  color: #929aa5;
  font-size: clamp(10px, 0.6vw, 12px);
  text-align: center;
}

/* FULL-SCREEN GALLERY */

.photo-lightbox-overlay {
  position: fixed;
  inset: 0;
  z-index: 9999;
  display: flex;
  align-items: center;
  justify-content: center;
  padding: clamp(18px, 2vw, 36px);
  background: rgba(4, 11, 22, 0.95);
  backdrop-filter: blur(8px);
}

.photo-lightbox-container {
  position: relative;
  width: min(1500px, 96vw);
  height: min(900px, 92vh);
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  gap: clamp(12px, 1vw, 18px);
}

.photo-lightbox-image {
  width: 100%;
  height: calc(
    100% - clamp(90px, 7vw, 120px)
  );
  display: flex;
  align-items: center;
  justify-content: center;
}

.photo-lightbox-image img {
  width: 100%;
  height: 100%;
  display: block;
  object-fit: contain;
}

.photo-lightbox-close {
  position: absolute;
  top: 0;
  right: 0;
  z-index: 5;
  width: clamp(42px, 2.6vw, 52px);
  height: clamp(42px, 2.6vw, 52px);
  display: flex;
  align-items: center;
  justify-content: center;
  padding: 0;
  color: #ffffff;
  background: rgba(255, 255, 255, 0.12);
  border: 1px solid rgba(255, 255, 255, 0.24);
  border-radius: 50%;
  cursor: pointer;
  transition:
    background 0.25s ease,
    transform 0.25s ease;
}

.photo-lightbox-close:hover {
  background: #1a51ad;
  transform: rotate(90deg);
}

.photo-lightbox-close svg {
  width: 21px;
  height: 21px;
  stroke-width: 1.8;
  stroke-linecap: round;
}

.photo-lightbox-count {
  position: absolute;
  top: 10px;
  left: 50%;
  z-index: 5;
  padding: 8px 15px;
  color: #ffffff;
  background: rgba(255, 255, 255, 0.12);
  border-radius: 20px;
  font-size: clamp(12px, 0.8vw, 15px);
  font-weight: 600;
  transform: translateX(-50%);
}

.photo-lightbox-arrow {
  position: absolute;
  top: 50%;
  z-index: 5;
  width: clamp(44px, 3vw, 58px);
  height: clamp(44px, 3vw, 58px);
  display: flex;
  align-items: center;
  justify-content: center;
  padding: 0;
  color: #173b73;
  background: rgba(255, 255, 255, 0.94);
  border: none;
  border-radius: 50%;
  cursor: pointer;
  transform: translateY(-50%);
  transition:
    color 0.25s ease,
    background 0.25s ease;
}

.photo-lightbox-arrow:hover {
  color: #ffffff;
  background: #1a51ad;
}

.photo-lightbox-arrow-left {
  left: clamp(8px, 1vw, 18px);
}

.photo-lightbox-arrow-right {
  right: clamp(8px, 1vw, 18px);
}

.photo-lightbox-thumbnails {
  width: 100%;
  height: clamp(72px, 5vw, 96px);
  display: flex;
  justify-content: center;
  gap: clamp(7px, 0.55vw, 11px);
  overflow-x: auto;
}

.photo-lightbox-thumbnail {
  width: clamp(90px, 7vw, 130px);
  min-width: clamp(90px, 7vw, 130px);
  height: 100%;
  padding: 0;
  overflow: hidden;
  background: transparent;
  border: 2px solid transparent;
  border-radius: clamp(8px, 0.55vw, 12px);
  cursor: pointer;
  opacity: 0.55;
  transition:
    opacity 0.2s ease,
    border-color 0.2s ease;
}

.photo-lightbox-thumbnail:hover,
.photo-lightbox-thumbnail.active {
  opacity: 1;
  border-color: #ffffff;
}

.photo-lightbox-thumbnail img {
  width: 100%;
  height: 100%;
  display: block;
  object-fit: cover;
}

/* TRANSITIONS */

.room-modal-enter-active,
.room-modal-leave-active {
  transition: opacity 0.3s ease;
}

.room-modal-enter-active .room-modal-container,
.room-modal-leave-active .room-modal-container {
  transition:
    opacity 0.3s ease,
    transform 0.3s ease;
}

.room-modal-enter-from,
.room-modal-leave-to {
  opacity: 0;
}

.room-modal-enter-from .room-modal-container {
  opacity: 0;
  transform:
    translateY(30px)
    scale(0.97);
}

.room-modal-leave-to .room-modal-container {
  opacity: 0;
  transform:
    translateY(20px)
    scale(0.98);
}

.gallery-switch-enter-active,
.gallery-switch-leave-active {
  transition: opacity 0.2s ease;
}

.gallery-switch-enter-from,
.gallery-switch-leave-to {
  opacity: 0;
}

.photo-lightbox-enter-active,
.photo-lightbox-leave-active {
  transition: opacity 0.25s ease;
}

.photo-lightbox-enter-from,
.photo-lightbox-leave-to {
  opacity: 0;
}

/* TABLET */

@media (max-width: 1000px) {
  .room-gallery-grid {
    grid-template-columns:
      minmax(0, 1.15fr)
      minmax(300px, 1fr);

    height: 440px;
  }
}

@media (max-width: 900px) {
  .room-modal-overlay {
    padding: 20px;
  }

  .room-modal-container {
    width: 100%;
    max-height: calc(100vh - 40px);
  }

  .room-modal-content {
    grid-template-columns: 1fr;

    gap: 30px;
    padding: 32px;
  }

  .room-booking-card {
    position: static;
  }
}

/* MOBILE */

@media (max-width: 700px) {
  .room-modal-overlay {
    align-items: flex-end;
    padding: 0;
  }

  .room-modal-container {
    width: 100%;
    max-height: 94vh;

    border-radius: 20px 20px 0 0;
  }

  .room-modal-images {
    padding: 8px;

    border-radius: 20px 20px 0 0;
  }

  .room-gallery-grid {
    grid-template-columns:
      repeat(2, minmax(0, 1fr));

    grid-template-rows:
      250px
      120px
      120px;

    gap: 6px;

    height: auto;
  }

  .room-gallery-main {
    grid-column: 1 / -1;

    border-radius: 14px 14px 0 0;
  }

  .room-gallery-side {
    display: contents;
  }

  .room-gallery-small {
    border-radius: 0;
  }

  .room-gallery-small:nth-child(3) {
    border-radius: 0 0 0 14px;
  }

  .room-gallery-small:nth-child(4) {
    border-radius: 0 0 14px 0;
  }

  .show-all-photos {
    right: 10px;
    bottom: 10px;

    min-height: 34px;

    padding: 0 11px;

    font-size: 11px;
  }

  .room-modal-content {
    gap: 26px;
    padding: 26px 20px;
  }

  .room-information,
  .facilities-grid,
  .room-policies {
    grid-template-columns: 1fr;
  }

  .room-modal-details h2 {
    font-size: 28px;
  }

  .room-detail-section h3 {
    font-size: 19px;
  }

  .room-detail-section p {
    font-size: 14px;
  }

  .room-booking-card {
    padding: 22px;
  }

  .photo-lightbox-overlay {
    padding: 10px;
  }

  .photo-lightbox-container {
    width: 100%;
    height: 94vh;
  }

  .photo-lightbox-image {
    height: calc(100% - 88px);
  }

  .photo-lightbox-arrow {
    width: 40px;
    height: 40px;
  }

  .photo-lightbox-thumbnails {
    justify-content: flex-start;
  }
}

/* =========================
   SMALL MOBILE
========================= */

@media (max-width: 420px) {
  .room-gallery-grid {
    grid-template-rows:
      220px
      100px
      100px;
  }

  .show-all-photos {
    max-width: 135px;
    font-size: 10px;
  }

  .room-modal-close {
    top: 13px;
    right: 13px;

    width: 40px;
    height: 40px;
  }

  .room-modal-content {
    padding: 24px 16px;
  }
}

@media (prefers-reduced-motion: reduce) {
  .room-modal-container,
  .room-modal-close,
  .room-gallery-item img,
  .show-all-photos,
  .room-booking-button,
  .photo-lightbox-close,
  .photo-lightbox-arrow {
    transition: none;
  }
}
</style>