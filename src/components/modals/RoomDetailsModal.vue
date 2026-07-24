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

          <div class="room-modal-images">
            <div class="room-modal-main-image">
              <img
                :src="activeImage"
                :alt="room.name"
              />
            </div>

            <div
              v-if="roomImages.length > 1"
              class="room-modal-thumbnails"
            >
              <button
                v-for="(image, index) in roomImages"
                :key="`${room.id}-${index}`"
                type="button"
                class="room-thumbnail"
                :class="{ active: activeImage === image }"
                @click="activeImage = image"
              >
                <img
                  :src="image"
                  :alt="`${room.name} image ${index + 1}`"
                />
              </button>
            </div>
          </div>

          <div class="room-modal-content">
            <div class="room-modal-details">
              <p
                v-if="room.subtitle"
                class="room-modal-eyebrow"
              >
                {{ room.subtitle }}
              </p>

              <h2 :id="`room-title-${room.id}`">
                {{ room.name }}
              </h2>

              <div class="room-information">
                <div class="room-information-item">
                  <span>Bed type</span>
                  <strong>{{ room.bed }}</strong>
                </div>

                <div class="room-information-item">
                  <span>Guests</span>
                  <strong>Up to {{ room.guests }} guests</strong>
                </div>

                <div class="room-information-item">
                  <span>Room size</span>
                  <strong>{{ room.size }}</strong>
                </div>

                <div
                  v-if="room.view"
                  class="room-information-item"
                >
                  <span>View</span>
                  <strong>{{ room.view }}</strong>
                </div>
              </div>

              <section
                v-if="room.description"
                class="room-detail-section"
              >
                <h3>About this room</h3>
                <p>{{ room.description }}</p>
              </section>

              <section
                v-if="room.facilities?.length"
                class="room-detail-section"
              >
                <h3>Room facilities</h3>

                <div class="facilities-grid">
                  <div
                    v-for="facility in room.facilities"
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

              <section
                v-if="room.bathroomFacilities?.length"
                class="room-detail-section"
              >
                <h3>Bathroom facilities</h3>

                <div class="facilities-grid">
                  <div
                    v-for="facility in room.bathroomFacilities"
                    :key="facility"
                    class="facility-item"
                  >
                    <span class="facility-check">✓</span>
                    <span>{{ facility }}</span>
                  </div>
                </div>
              </section>

              <section
                v-if="room.included?.length"
                class="room-detail-section"
              >
                <h3>Included with your stay</h3>

                <div class="included-list">
                  <div
                    v-for="item in room.included"
                    :key="item"
                    class="included-item"
                  >
                    <span class="facility-check">✓</span>
                    <span>{{ item }}</span>
                  </div>
                </div>
              </section>

              <section class="room-detail-section">
                <h3>Room policies</h3>

                <div class="room-policies">
                  <div v-if="room.checkIn">
                    <span>Check-in</span>
                    <strong>{{ room.checkIn }}</strong>
                  </div>

                  <div v-if="room.checkOut">
                    <span>Check-out</span>
                    <strong>{{ room.checkOut }}</strong>
                  </div>

                  <div v-if="room.smoking">
                    <span>Smoking</span>
                    <strong>{{ room.smoking }}</strong>
                  </div>
                </div>
              </section>
            </div>

            <aside class="room-booking-card">
              <span class="booking-label">
                Starting from
              </span>

              <div class="booking-price">
                <strong>
                  $ {{ formatPrice(room.price) }}
                </strong>
                <span>/ night</span>
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
  }
});

const emit = defineEmits([
  "close",
  "reserve"
]);

const activeImage = ref("");

const roomImages = computed(() => {
  if (!props.room) {
    return [];
  }

  if (
    Array.isArray(props.room.images) &&
    props.room.images.length
  ) {
    return props.room.images;
  }

  return props.room.image
    ? [props.room.image]
    : [];
});

watch(
  () => [props.isOpen, props.room],
  () => {
    if (props.isOpen && props.room) {
      activeImage.value =
        roomImages.value[0] || "";

      document.body.style.overflow = "hidden";
    } else {
      document.body.style.overflow = "";
    }
  },
  {
    immediate: true
  }
);

function closeModal() {
  emit("close");
}

function reserveRoom() {
  emit("reserve", props.room);
}

function formatPrice(price) {
  return new Intl.NumberFormat("en-LK").format(
    Number(price) || 0
  );
}

function handleKeydown(event) {
  if (
    event.key === "Escape" &&
    props.isOpen
  ) {
    closeModal();
  }
}

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

/* =========================
   MODAL OVERLAY
========================= */

.room-modal-overlay {
  position: fixed;
  inset: 0;
  z-index: 5000;

  display: flex;
  align-items: center;
  justify-content: center;

  padding: clamp(24px, 2vw, 42px);

  background: rgba(7, 18, 34, 0.72);
  backdrop-filter: blur(clamp(5px, 0.4vw, 8px));
}

/* =========================
   MODAL CONTAINER
========================= */

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
    0 clamp(30px, 2vw, 42px)
    clamp(90px, 5vw, 110px)
    rgba(0, 0, 0, 0.3);

  scrollbar-width: thin;
  scrollbar-color: #b8c4d2 transparent;
}

.room-modal-container::-webkit-scrollbar {
  width: 7px;
}

.room-modal-container::-webkit-scrollbar-track {
  background: transparent;
}

.room-modal-container::-webkit-scrollbar-thumb {
  background: #b8c4d2;
  border-radius: 10px;
}

/* =========================
   CLOSE BUTTON
========================= */

.room-modal-close {
  position: absolute;
  top: clamp(18px, 1.1vw, 24px);
  right: clamp(18px, 1.1vw, 24px);
  z-index: 10;

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
    0 5px clamp(18px, 1.2vw, 25px)
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

/* =========================
   IMAGE GALLERY
========================= */

.room-modal-images {
  padding: clamp(18px, 1.15vw, 24px);

  background: #f3f6fa;

  border-radius:
    clamp(22px, 1.4vw, 28px)
    clamp(22px, 1.4vw, 28px)
    0
    0;
}

.room-modal-main-image {
  width: 100%;
  height: clamp(430px, 29vw, 560px);

  overflow: hidden;

  background: #e8edf4;
  border-radius: clamp(15px, 1vw, 20px);
}

.room-modal-main-image img {
  width: 100%;
  height: 100%;

  display: block;

  object-fit: cover;

  transition: transform 0.45s ease;
}

.room-modal-main-image:hover img {
  transform: scale(1.015);
}

/* =========================
   THUMBNAILS
========================= */

.room-modal-thumbnails {
  display: grid;
  grid-template-columns: repeat(4, minmax(0, 1fr));

  gap: clamp(10px, 0.7vw, 14px);
  margin-top: clamp(10px, 0.7vw, 14px);
}

.room-thumbnail {
  height: clamp(88px, 5.5vw, 110px);

  padding: 0;
  overflow: hidden;

  background: transparent;

  border: 2px solid transparent;
  border-radius: clamp(10px, 0.65vw, 14px);

  cursor: pointer;

  opacity: 0.65;

  transition:
    opacity 0.2s ease,
    border-color 0.2s ease,
    transform 0.2s ease;
}

.room-thumbnail:hover,
.room-thumbnail.active {
  opacity: 1;
  border-color: #2d6adc;
  transform: translateY(-2px);
}

.room-thumbnail img {
  width: 100%;
  height: 100%;

  display: block;
  object-fit: cover;
}

/* =========================
   MAIN CONTENT
========================= */

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

/* =========================
   HEADING
========================= */

.room-modal-eyebrow {
  margin: 0 0 clamp(8px, 0.5vw, 11px);

  color: #2d6adc;

  font-size: clamp(12px, 0.72vw, 14px);
  font-weight: 700;

  letter-spacing: clamp(1.2px, 0.08vw, 1.5px);
  text-transform: uppercase;
}

.room-modal-details h2 {
  margin: 0;

  color: #1a51ad;

  font-size: clamp(30px, 2.4vw, 46px);
  font-weight: 600;
  line-height: 1.2;
}

/* =========================
   ROOM INFORMATION
========================= */

.room-information {
  display: grid;
  grid-template-columns: repeat(2, minmax(0, 1fr));

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

  transition:
    border-color 0.25s ease,
    transform 0.25s ease,
    box-shadow 0.25s ease;
}

.room-information-item:hover {
  border-color: #d5e1f2;

  transform: translateY(-2px);

  box-shadow:
    0 8px 20px
    rgba(28, 55, 89, 0.07);
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

  margin-top: clamp(3px, 0.2vw, 5px);

  color: #25364c;

  font-size: clamp(13px, 0.78vw, 15px);
  font-weight: 600;
}

/* =========================
   DETAIL SECTIONS
========================= */

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

/* =========================
   FACILITIES
========================= */

.facilities-grid {
  display: grid;
  grid-template-columns: repeat(2, minmax(0, 1fr));

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

  font-size: clamp(10px, 0.6vw, 12px);
  font-weight: 700;
}

/* =========================
   INCLUDED LIST
========================= */

.included-list {
  display: grid;

  gap: clamp(11px, 0.7vw, 14px);
}

/* =========================
   POLICIES
========================= */

.room-policies {
  display: grid;
  grid-template-columns: repeat(3, minmax(0, 1fr));

  gap: clamp(12px, 0.8vw, 16px);
}

.room-policies > div {
  padding: clamp(14px, 0.9vw, 18px);

  background: #f6f8fb;

  border: 1px solid #edf0f4;
  border-radius: clamp(10px, 0.65vw, 14px);

  transition:
    border-color 0.25s ease,
    transform 0.25s ease;
}

.room-policies > div:hover {
  border-color: #d5e1f2;
  transform: translateY(-2px);
}

/* =========================
   BOOKING CARD
========================= */

.room-booking-card {
  position: sticky;
  top: clamp(20px, 1.25vw, 26px);

  align-self: start;

  padding: clamp(25px, 1.5vw, 34px);

  background: #ffffff;

  border: 1px solid #dfe5ed;
  border-radius: clamp(15px, 0.95vw, 19px);

  box-shadow:
    0 clamp(12px, 0.8vw, 17px)
    clamp(35px, 2.2vw, 46px)
    rgba(28, 55, 89, 0.1);
}

/* =========================
   BOOKING PRICE
========================= */

.booking-label {
  color: #7c8693;

  font-size: clamp(12px, 0.72vw, 14px);
}

.booking-price {
  padding-bottom: clamp(20px, 1.25vw, 26px);

  margin-top: clamp(5px, 0.3vw, 7px);
  margin-bottom: clamp(20px, 1.25vw, 26px);

  border-bottom: 1px solid #e7ebf0;
}

.booking-price strong {
  color: #1a51ad;

  font-size: clamp(25px, 1.55vw, 32px);
  font-weight: 700;
}

.booking-price span {
  margin-left: clamp(4px, 0.25vw, 6px);

  color: #7c8693;

  font-size: clamp(11px, 0.67vw, 13px);
}

/* =========================
   BOOKING BENEFITS
========================= */

.booking-benefits {
  color: #56616e;

  font-size: clamp(12px, 0.72vw, 14px);
}

.booking-benefits p {
  margin: 0 0 clamp(10px, 0.65vw, 13px);
}

/* =========================
   BOOKING BUTTON
========================= */

.room-booking-button {
  width: 100%;
  min-height: clamp(46px, 2.8vw, 56px);

  margin-top: clamp(12px, 0.8vw, 17px);
  padding: 0 clamp(16px, 1vw, 21px);

  color: #ffffff;
  background: #1a51ad;

  border: none;
  border-radius: clamp(9px, 0.6vw, 12px);

  font-family: inherit;

  font-size: clamp(13px, 0.78vw, 15px);
  font-weight: 700;

  cursor: pointer;

  transition:
    background 0.25s ease,
    transform 0.25s ease,
    box-shadow 0.25s ease;
}

.room-booking-button:hover {
  background: #103f8d;

  transform: translateY(-1px);

  box-shadow:
    0 10px 22px
    rgba(26, 81, 173, 0.22);
}

.booking-message {
  margin: clamp(12px, 0.75vw, 15px) 0 0;

  color: #929aa5;

  font-size: clamp(10px, 0.6vw, 12px);
  text-align: center;
}

/* =========================
   MODAL TRANSITION
========================= */

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

/* =========================
   LARGE SCREEN
========================= */

@media (min-width: 1921px) {
  .room-modal-container {
    width: min(1380px, calc(100vw - 100px));
  }

  .room-modal-main-image {
    height: 560px;
  }

  .room-modal-content {
    grid-template-columns:
      minmax(0, 1fr)
      360px;

    gap: 50px;
    padding: 52px;
  }

  .room-modal-details h2 {
    font-size: 46px;
  }

  .room-booking-card {
    padding: 34px;
  }
}

/* =========================
   TABLET
========================= */

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

/* =========================
   MOBILE
========================= */

@media (max-width: 650px) {
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
    padding: 10px;

    border-radius: 20px 20px 0 0;
  }

  .room-modal-main-image {
    height: 280px;
  }

  .room-modal-thumbnails {
    display: flex;

    overflow-x: auto;

    padding-bottom: 3px;
  }

  .room-thumbnail {
    width: 85px;
    min-width: 85px;
    height: 68px;
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

  .facility-item,
  .included-item {
    font-size: 13px;
  }

  .room-booking-card {
    padding: 22px;
  }
}

/* =========================
   SMALL MOBILE
========================= */

@media (max-width: 420px) {
  .room-modal-main-image {
    height: 230px;
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

  .room-information {
    margin: 24px 0;
  }
}

/* =========================
   REDUCED MOTION
========================= */

@media (prefers-reduced-motion: reduce) {
  .room-modal-container,
  .room-modal-close,
  .room-modal-main-image img,
  .room-thumbnail,
  .room-information-item,
  .room-policies > div,
  .room-booking-button {
    transition: none;
  }
}
</style>