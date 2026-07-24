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

              <p class="booking-message">
                You will not be charged at this stage.
              </p>
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
.room-modal-overlay {
  position: fixed;
  inset: 0;
  z-index: 5000;

  display: flex;
  align-items: center;
  justify-content: center;

  padding: 30px;

  background: rgba(7, 18, 34, 0.72);
  backdrop-filter: blur(5px);
}

.room-modal-container {
  position: relative;

  width: min(1180px, 100%);
  max-height: calc(100vh - 60px);

  overflow-y: auto;

  background: #ffffff;
  border-radius: 22px;

  box-shadow:
    0 30px 90px rgba(0, 0, 0, 0.3);
}

.room-modal-close {
  position: absolute;
  top: 18px;
  right: 18px;
  z-index: 10;

  width: 44px;
  height: 44px;

  display: flex;
  align-items: center;
  justify-content: center;

  color: #1a51ad;
  background: #ffffff;

  border: none;
  border-radius: 50%;

  cursor: pointer;

  box-shadow:
    0 5px 18px rgba(0, 0, 0, 0.18);

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
  width: 21px;
  height: 21px;

  stroke-width: 1.8;
  stroke-linecap: round;
}

.room-modal-images {
  padding: 18px;

  background: #f3f6fa;
  border-radius: 22px 22px 0 0;
}

.room-modal-main-image {
  width: 100%;
  height: 430px;

  overflow: hidden;
  border-radius: 15px;
}

.room-modal-main-image img {
  width: 100%;
  height: 100%;

  display: block;
  object-fit: cover;
}

.room-modal-thumbnails {
  display: grid;
  grid-template-columns:
    repeat(4, minmax(0, 1fr));

  gap: 10px;
  margin-top: 10px;
}

.room-thumbnail {
  height: 88px;
  padding: 0;

  overflow: hidden;

  background: transparent;
  border: 2px solid transparent;
  border-radius: 10px;

  cursor: pointer;
  opacity: 0.65;

  transition:
    opacity 0.2s ease,
    border-color 0.2s ease;
}

.room-thumbnail:hover,
.room-thumbnail.active {
  opacity: 1;
  border-color: #2d6adc;
}

.room-thumbnail img {
  width: 100%;
  height: 100%;

  display: block;
  object-fit: cover;
}

.room-modal-content {
  display: grid;
  grid-template-columns:
    minmax(0, 1fr) 310px;

  gap: 38px;
  padding: 38px;
}

.room-modal-eyebrow {
  margin: 0 0 8px;

  color: #2d6adc;

  font-size: 12px;
  font-weight: 700;
  letter-spacing: 1.2px;

  text-transform: uppercase;
}

.room-modal-details h2 {
  margin: 0;

  color: #1a51ad;

  font-size: clamp(28px, 3vw, 39px);
  line-height: 1.2;
}

.room-information {
  display: grid;
  grid-template-columns:
    repeat(2, minmax(0, 1fr));

  gap: 14px;
  margin: 28px 0;
}

.room-information-item {
  padding: 15px;

  background: #f6f8fb;
  border-radius: 10px;
}

.room-information-item span,
.room-policies span {
  display: block;

  color: #87909c;

  font-size: 11px;
}

.room-information-item strong,
.room-policies strong {
  display: block;

  margin-top: 3px;

  color: #25364c;

  font-size: 13px;
}

.room-detail-section {
  padding: 25px 0;

  border-top: 1px solid #e8edf2;
}

.room-detail-section h3 {
  margin: 0 0 15px;

  color: #25364c;

  font-size: 19px;
}

.room-detail-section p {
  margin: 0;

  color: #626d7a;

  font-size: 14px;
  line-height: 1.8;
}

.facilities-grid {
  display: grid;
  grid-template-columns:
    repeat(2, minmax(0, 1fr));

  gap: 12px 20px;
}

.facility-item,
.included-item {
  display: flex;
  align-items: center;

  gap: 9px;

  color: #56616e;

  font-size: 13px;
}

.facility-icon {
  width: 30px;
  height: 30px;

  display: inline-flex;
  align-items: center;
  justify-content: center;

  flex-shrink: 0;

  color: #1a51ad;
  background: #edf3fb;

  border-radius: 8px;
  font-size: 12px;
}

.included-list {
  display: grid;
  gap: 11px;
}

.room-policies {
  display: grid;
  grid-template-columns:
    repeat(3, minmax(0, 1fr));

  gap: 12px;
}

.room-policies > div {
  padding: 14px;

  background: #f6f8fb;
  border-radius: 10px;
}

.room-booking-card {
  position: sticky;
  top: 20px;

  align-self: start;

  padding: 25px;

  border: 1px solid #dfe5ed;
  border-radius: 15px;

  box-shadow:
    0 12px 35px rgba(28, 55, 89, 0.1);
}

.booking-label {
  color: #7c8693;
  font-size: 12px;
}

.booking-price {
  padding-bottom: 20px;
  margin-top: 5px;
  margin-bottom: 20px;

  border-bottom: 1px solid #e7ebf0;
}

.booking-price strong {
  color: #1a51ad;
  font-size: 25px;
}

.booking-price span {
  margin-left: 4px;

  color: #7c8693;
  font-size: 11px;
}

.booking-benefits {
  color: #56616e;
  font-size: 12px;
}

.booking-benefits p {
  margin: 0 0 10px;
}

.room-booking-button {
  width: 100%;
  min-height: 46px;

  margin-top: 12px;

  color: #ffffff;
  background: #1a51ad;

  border: none;
  border-radius: 9px;

  font-family: inherit;
  font-size: 13px;
  font-weight: 700;

  cursor: pointer;

  transition: background 0.25s ease;
}

.room-booking-button:hover {
  background: #103f8d;
}

.booking-message {
  margin: 12px 0 0;

  color: #929aa5;

  font-size: 10px;
  text-align: center;
}

.room-modal-enter-active,
.room-modal-leave-active {
  transition: opacity 0.3s ease;
}

.room-modal-enter-active
.room-modal-container,
.room-modal-leave-active
.room-modal-container {
  transition:
    opacity 0.3s ease,
    transform 0.3s ease;
}

.room-modal-enter-from,
.room-modal-leave-to {
  opacity: 0;
}

.room-modal-enter-from
.room-modal-container {
  opacity: 0;
  transform:
    translateY(30px) scale(0.97);
}

.room-modal-leave-to
.room-modal-container {
  opacity: 0;
  transform:
    translateY(20px) scale(0.98);
}

@media (max-width: 900px) {
  .room-modal-content {
    grid-template-columns: 1fr;
  }

  .room-booking-card {
    position: static;
  }
}

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
  }

  .room-thumbnail {
    width: 85px;
    min-width: 85px;
    height: 68px;
  }

  .room-modal-content {
    padding: 26px 20px;
  }

  .room-information,
  .facilities-grid,
  .room-policies {
    grid-template-columns: 1fr;
  }
}

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
}
</style>