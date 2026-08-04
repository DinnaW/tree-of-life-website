<template>
  <main v-if="pkg" class="details-page">
    <!-- TOP NAVIGATION -->
    <div class="details-container">
      <button type="button" class="back-button" @click="emit('back')">
        <Icon icon="lucide:arrow-left" />
        Back to packages
      </button>
    </div>

    <!-- HERO -->
    <section class="details-hero">
      <div class="details-container hero-grid">
        <div class="hero-content">
          <span class="category-label">
            {{ pkg.category }}
          </span>

          <h1>{{ pkg.title }}</h1>

          <p class="hero-subtitle">
            {{ pkg.subtitle || pkg.description }}
          </p>

          <div class="hero-meta">
            <span>
              <Icon icon="lucide:star" class="star-icon" />
              <strong>{{ pkg.rating }}</strong>
              Guest rating
            </span>

            <span>
              <Icon icon="lucide:moon" />
              {{ pkg.stay }}
            </span>

            <span>
              <Icon icon="lucide:users" />
              {{ pkg.guests }}
            </span>

            <span>
              <Icon icon="lucide:bed-double" />
              {{ pkg.room }}
            </span>
          </div>
        </div>

        <div class="hero-image">
          <img :src="pkg.image" :alt="pkg.title" />

          <button
            type="button"
            class="hero-gallery-button"
            @click="openGallery"
          >
            <Icon icon="lucide:images" />
            View all {{ pkg.gallery?.length || 1 }} photos
          </button>
        </div>
      </div>
    </section>

    <!-- DETAILS -->
    <section class="details-content">
      <div class="details-container details-layout">
        <div class="details-main">
          <!-- ABOUT -->
          <article class="details-section">
            <span class="section-eyebrow">Package overview</span>

            <h2>About this experience</h2>

            <p class="description">
              {{ pkg.longDescription || pkg.description }}
            </p>
          </article>

          <!-- INCLUDED -->
          <article class="details-section">
            <span class="section-eyebrow">Everything prepared</span>

            <h2>What's included</h2>

            <div class="included-grid">
              <div
                v-for="item in pkg.highlights || pkg.includes"
                :key="item"
                class="included-item"
              >
                <span class="included-icon">
                  <Icon icon="lucide:check" />
                </span>

                <span>{{ item }}</span>
              </div>
            </div>
          </article>

          <!-- SCHEDULE -->
          <article
            v-if="pkg.schedule && pkg.schedule.length"
            class="details-section"
          >
            <span class="section-eyebrow">Your experience</span>

            <h2>Package itinerary</h2>

            <div class="schedule">
              <div
                v-for="(item, index) in pkg.schedule"
                :key="`${item.time}-${index}`"
                class="schedule-item"
              >
                <div class="schedule-marker">
                  <span></span>
                </div>

                <div class="schedule-time">
                  {{ item.time }}
                </div>

                <div class="schedule-content">
                  <h3>{{ item.title }}</h3>
                  <p>{{ item.text }}</p>
                </div>
              </div>
            </div>
          </article>

          <!-- POLICIES -->
          <article
            v-if="pkg.policies && pkg.policies.length"
            class="details-section"
          >
            <span class="section-eyebrow">Before your stay</span>

            <h2>Package information</h2>

            <ul class="policy-list">
              <li v-for="policy in pkg.policies" :key="policy">
                <Icon icon="lucide:info" />
                <span>{{ policy }}</span>
              </li>
            </ul>
          </article>
        </div>

        <!-- BOOKING CARD -->
        <aside class="booking-card">
          <div class="booking-price">
            <span>Package price</span>

            <div>
              <strong>${{ pkg.price }}</strong>
              <small>per package</small>
            </div>
          </div>

          <div class="booking-divider"></div>

          <div class="booking-info">
            <div>
              <span>
                <Icon icon="lucide:moon" />
                Duration
              </span>

              <strong>{{ pkg.stay }}</strong>
            </div>

            <div>
              <span>
                <Icon icon="lucide:users" />
                Guests
              </span>

              <strong>{{ pkg.guests }}</strong>
            </div>

            <div>
              <span>
                <Icon icon="lucide:bed-double" />
                Room
              </span>

              <strong>{{ pkg.room }}</strong>
            </div>
          </div>

          <button
            type="button"
            class="book-button"
            @click="emit('book', pkg)"
          >
            Book this package
            <Icon icon="lucide:arrow-right" />
          </button>

          <p class="booking-note">
            <Icon icon="lucide:shield-check" />
            Secure reservation with no hidden booking fees.
          </p>
        </aside>
      </div>
    </section>

    <!-- GALLERY POPUP -->
    <Teleport to="body">
      <Transition name="gallery">
        <div
          v-if="galleryOpen"
          class="gallery-modal"
          @click.self="closeGallery"
        >
          <div class="gallery-dialog">
            <header class="gallery-header">
              <div>
                <span>{{ pkg.category }}</span>
                <h2>{{ pkg.title }}</h2>
              </div>

              <button
                type="button"
                aria-label="Close gallery"
                @click="closeGallery"
              >
                <Icon icon="lucide:x" />
              </button>
            </header>

            <div class="gallery-stage">
              <img
                :src="currentImage"
                :alt="`${pkg.title} image ${galleryIndex + 1}`"
              />

              <button
                v-if="(pkg.gallery?.length || 0) > 1"
                type="button"
                class="gallery-arrow gallery-arrow-left"
                @click="previousImage"
              >
                <Icon icon="lucide:chevron-left" />
              </button>

              <button
                v-if="(pkg.gallery?.length || 0) > 1"
                type="button"
                class="gallery-arrow gallery-arrow-right"
                @click="nextImage"
              >
                <Icon icon="lucide:chevron-right" />
              </button>

              <span class="gallery-count">
                {{ galleryIndex + 1 }} / {{ pkg.gallery?.length || 1 }}
              </span>
            </div>

            <div class="gallery-thumbnails">
              <button
                v-for="(image, index) in (pkg.gallery || [pkg.image])"
                :key="`${image}-${index}`"
                type="button"
                :class="{ active: galleryIndex === index }"
                @click="galleryIndex = index"
              >
                <img
                  :src="image"
                  :alt="`${pkg.title} thumbnail ${index + 1}`"
                />
              </button>
            </div>
          </div>
        </div>
      </Transition>
    </Teleport>
  </main>
</template>

<script setup>
import {
  computed,
  onBeforeUnmount,
  onMounted,
  ref,
  watch
} from "vue";

import { Icon } from "@iconify/vue";

const props = defineProps({
  pkg: {
    type: Object,
    required: true
  }
});

const emit = defineEmits(["back", "book"]);

const galleryOpen = ref(false);
const galleryIndex = ref(0);

const currentImage = computed(() => {
  return props.pkg?.gallery?.[galleryIndex.value] || props.pkg?.image || "";
});

function openGallery() {
  galleryIndex.value = 0;
  galleryOpen.value = true;
  document.body.style.overflow = "hidden";
}

function closeGallery() {
  galleryOpen.value = false;
  document.body.style.overflow = "";
}

function nextImage() {
  const total = props.pkg?.gallery?.length || 0;

  if (!total) return;

  galleryIndex.value = (galleryIndex.value + 1) % total;
}

function previousImage() {
  const total = props.pkg?.gallery?.length || 0;

  if (!total) return;

  galleryIndex.value =
    (galleryIndex.value - 1 + total) % total;
}

function handleKeyboard(event) {
  if (!galleryOpen.value) return;

  if (event.key === "Escape") {
    closeGallery();
  }

  if (event.key === "ArrowRight") {
    nextImage();
  }

  if (event.key === "ArrowLeft") {
    previousImage();
  }
}

watch(
  () => props.pkg?.id,
  () => {
    galleryIndex.value = 0;
    window.scrollTo({
      top: 0,
      behavior: "smooth"
    });
  }
);

onMounted(() => {
  window.addEventListener("keydown", handleKeyboard);
});

onBeforeUnmount(() => {
  window.removeEventListener("keydown", handleKeyboard);
  document.body.style.overflow = "";
});
</script>

<style scoped>
.details-page,
.details-page * {
  box-sizing: border-box;
}

.details-page {
  --primary: #134a8e;
  --primary-dark: #0c376e;
  --accent: #2476c8;
  --background: #f5f8fc;
  --text: #1d2735;
  --muted: #6d7888;
  --border: #e0e7ef;
  --white: #ffffff;

  min-height: 100vh;
  background: var(--background);
  color: var(--text);
  font-family: 'Figtree';
}

.details-container {
  width: min(92%, max(1200px, 83.3333vw));
  margin: 0 auto;
}

.back-button {
  display: inline-flex;
  align-items: center;
  gap: max(8px, 0.5556vw);
  margin: max(28px, 1.9444vw) 0;
  padding: 0;
  border: 0;
  background: transparent;
  color: #586778;
  cursor: pointer;
  font-size: max(13px, 0.9028vw);
  font-weight: 600;
}

.back-button:hover {
  color: var(--primary);
}

.details-hero {
  padding-bottom: max(55px, 3.8194vw);
}

.hero-grid {
  display: grid;
  grid-template-columns: minmax(0, 0.82fr) minmax(max(480px, 33.3333vw), 1.18fr);
  align-items: center;
  gap: clamp(max(40px, 2.7778vw), 6vw, max(80px, 5.5556vw));
}

.hero-content {
  padding: max(20px, 1.3889vw) 0;
}

.category-label {
  display: inline-flex;
  padding: max(8px, 0.5556vw) max(14px, 0.9722vw);
  border: max(1px, 0.0694vw) solid #d7e6f6;
  border-radius: max(999px, 69.375vw);
  background: #edf5fd;
  color: var(--primary);
  font-size: max(10px, 0.6944vw);
  font-weight: 700;
  letter-spacing: max(1px, 0.0694vw);
  text-transform: uppercase;
}

.hero-content h1 {
  max-width: max(600px, 41.6667vw);
  margin: max(20px, 1.3889vw) 0 max(14px, 0.9722vw);
  color: #0f3e82;
  font-size: clamp(max(36px, 2.5vw), 4.5vw, max(58px, 4.0278vw));
  font-weight: 600;
  line-height: 1.08;
  letter-spacing: -1.5px;
}

.hero-subtitle {
  max-width: max(570px, 39.5833vw);
  margin: 0;
  color: var(--muted);
  font-size: max(15px, 1.0417vw);
  line-height: 1.8;
}

.hero-meta {
  display: flex;
  flex-wrap: wrap;
  gap: max(12px, 0.8333vw) max(22px, 1.5278vw);
  margin-top: max(28px, 1.9444vw);
}

.hero-meta span {
  display: inline-flex;
  align-items: center;
  gap: max(7px, 0.4861vw);
  color: #334155;
  font-size: max(12px, 0.8333vw);
  font-weight: 500;
}

.hero-meta svg {
  color: var(--accent);
  font-size: max(17px, 1.1806vw);
}

.hero-meta .star-icon {
  color: #e4a72b;
}

.hero-image {
  position: relative;
  height: clamp(max(390px, 27.0833vw), 42vw, max(560px, 38.8889vw));
  overflow: hidden;
  border-radius: max(22px, 1.5278vw);
  background: #dfe7ef;
  box-shadow: 0 max(24px, 1.6667vw) max(60px, 4.1667vw) rgba(25, 51, 82, 0.15);
}

.hero-image > img {
  width: 100%;
  height: 100%;
  display: block;
  object-fit: cover;
}

.hero-gallery-button {
  position: absolute;
  right: max(20px, 1.3889vw);
  bottom: max(20px, 1.3889vw);
  display: inline-flex;
  align-items: center;
  gap: max(8px, 0.5556vw);
  min-height: max(44px, 3.0556vw);
  padding: max(11px, 0.7639vw) max(16px, 1.1111vw);
  border: max(1px, 0.0694vw) solid rgba(255, 255, 255, 0.7);
  border-radius: max(9px, 0.625vw);
  background: rgba(255, 255, 255, 0.94);
  color: var(--primary);
  cursor: pointer;
  font-size: max(12px, 0.8333vw);
  font-weight: 700;
  box-shadow: 0 max(10px, 0.6944vw) max(25px, 1.7361vw) rgba(0, 0, 0, 0.15);
  backdrop-filter: blur(max(10px, 0.6944vw));
}

.details-content {
  padding: max(70px, 4.8611vw) 0 max(90px, 6.25vw);
  background: #ffffff;
}

.details-layout {
  display: grid;
  grid-template-columns: minmax(0, 1fr) max(345px, 23.9583vw);
  align-items: start;
  gap: clamp(max(50px, 3.4722vw), 7vw, max(90px, 6.25vw));
}

.details-section {
  padding: 0 0 max(54px, 3.75vw);
  margin-bottom: max(54px, 3.75vw);
  border-bottom: max(1px, 0.0694vw) solid var(--border);
}

.details-section:last-child {
  margin-bottom: 0;
}

.section-eyebrow {
  display: block;
  margin-bottom: max(9px, 0.625vw);
  color: var(--accent);
  font-size: max(10px, 0.6944vw);
  font-weight: 700;
  letter-spacing: max(1.4px, 0.0972vw);
  text-transform: uppercase;
}

.details-section h2 {
  margin: 0 0 max(18px, 1.25vw);
  color: #103f82;
  font-size: clamp(max(25px, 1.7361vw), 3vw, max(35px, 2.4306vw));
  font-weight: 600;
}

.description {
  max-width: max(760px, 52.7778vw);
  margin: 0;
  color: #5f6b78;
  font-size: max(14px, 0.9722vw);
  line-height: 1.9;
}

.included-grid {
  display: grid;
  grid-template-columns: repeat(2, minmax(0, 1fr));
  gap: max(14px, 0.9722vw) max(22px, 1.5278vw);
}

.included-item {
  display: flex;
  align-items: center;
  gap: max(11px, 0.7639vw);
  min-height: max(50px, 3.4722vw);
  padding: max(12px, 0.8333vw) max(14px, 0.9722vw);
  border: max(1px, 0.0694vw) solid #e5ebf2;
  border-radius: max(10px, 0.6944vw);
  background: #fafcff;
  color: #4b5765;
  font-size: max(13px, 0.9028vw);
}

.included-icon {
  width: max(28px, 1.9444vw);
  height: max(28px, 1.9444vw);
  display: grid;
  place-items: center;
  flex: 0 0 auto;
  border-radius: 50%;
  background: #e9f4ff;
  color: var(--accent);
}

.schedule {
  display: grid;
}

.schedule-item {
  position: relative;
  display: grid;
  grid-template-columns: max(26px, 1.8056vw) max(82px, 5.6944vw) 1fr;
  gap: max(16px, 1.1111vw);
  padding-bottom: max(30px, 2.0833vw);
}

.schedule-marker {
  position: relative;
  display: flex;
  justify-content: center;
}

.schedule-marker::after {
  content: "";
  position: absolute;
  top: max(17px, 1.1806vw);
  bottom: -17px;
  width: max(1px, 0.0694vw);
  background: #d8e1ea;
}

.schedule-item:last-child .schedule-marker::after {
  display: none;
}

.schedule-marker span {
  position: relative;
  z-index: 2;
  width: max(11px, 0.7639vw);
  height: max(11px, 0.7639vw);
  margin-top: max(4px, 0.2778vw);
  border: max(3px, 0.2083vw) solid #d9eaff;
  border-radius: 50%;
  background: var(--accent);
}

.schedule-time {
  padding-top: max(1px, 0.0694vw);
  color: var(--primary);
  font-size: max(12px, 0.8333vw);
  font-weight: 700;
}

.schedule-content h3 {
  margin: 0 0 max(6px, 0.4167vw);
  color: #273548;
  font-size: max(14px, 0.9722vw);
}

.schedule-content p {
  margin: 0;
  color: var(--muted);
  font-size: max(12px, 0.8333vw);
  line-height: 1.7;
}

.policy-list {
  display: grid;
  gap: max(13px, 0.9028vw);
  margin: 0;
  padding: 0;
  list-style: none;
}

.policy-list li {
  display: flex;
  align-items: flex-start;
  gap: max(10px, 0.6944vw);
  color: #5f6b78;
  font-size: max(13px, 0.9028vw);
  line-height: 1.6;
}

.policy-list svg {
  flex: 0 0 auto;
  margin-top: max(2px, 0.1389vw);
  color: var(--accent);
  font-size: max(17px, 1.1806vw);
}

.booking-card {
  position: sticky;
  top: max(100px, 6.9444vw);
  padding: max(27px, 1.875vw);
  border: max(1px, 0.0694vw) solid #dfe6ee;
  border-radius: max(17px, 1.1806vw);
  background: #ffffff;
  box-shadow: 0 max(18px, 1.25vw) max(45px, 3.125vw) rgba(29, 52, 79, 0.1);
}

.booking-price > span {
  color: var(--muted);
  font-size: max(11px, 0.7639vw);
}

.booking-price > div {
  display: flex;
  align-items: flex-end;
  gap: max(8px, 0.5556vw);
  margin-top: max(7px, 0.4861vw);
}

.booking-price strong {
  color: var(--primary);
  font-size: max(38px, 2.6389vw);
  line-height: 1;
}

.booking-price small {
  padding-bottom: max(4px, 0.2778vw);
  color: var(--muted);
  font-size: max(11px, 0.7639vw);
}

.booking-divider {
  height: max(1px, 0.0694vw);
  margin: max(24px, 1.6667vw) 0;
  background: var(--border);
}

.booking-info {
  display: grid;
  gap: max(18px, 1.25vw);
}

.booking-info > div {
  display: flex;
  justify-content: space-between;
  gap: max(14px, 0.9722vw);
}

.booking-info span {
  display: inline-flex;
  align-items: center;
  gap: max(7px, 0.4861vw);
  color: var(--muted);
  font-size: max(11px, 0.7639vw);
}

.booking-info svg {
  color: var(--accent);
  font-size: max(15px, 1.0417vw);
}

.booking-info strong {
  color: #273548;
  font-size: max(11px, 0.7639vw);
  text-align: right;
}

.book-button {
  width: 100%;
  min-height: max(50px, 3.4722vw);
  display: flex;
  align-items: center;
  justify-content: center;
  gap: max(9px, 0.625vw);
  margin-top: max(27px, 1.875vw);
  padding: max(13px, 0.9028vw) max(18px, 1.25vw);
  border: 0;
  border-radius: max(8px, 0.5556vw);
  background: var(--primary);
  color: #ffffff;
  cursor: pointer;
  font-size: max(13px, 0.9028vw);
  font-weight: 700;
  transition: 0.25s ease;
}

.book-button:hover {
  background: var(--primary-dark);
  transform: translateY(-2px);
}

.booking-note {
  display: flex;
  align-items: flex-start;
  gap: max(7px, 0.4861vw);
  margin: max(16px, 1.1111vw) 0 0;
  color: var(--muted);
  font-size: max(10px, 0.6944vw);
  line-height: 1.5;
}

.booking-note svg {
  flex: 0 0 auto;
  color: var(--accent);
  font-size: max(15px, 1.0417vw);
}

/* GALLERY */

.gallery-modal {
  position: fixed;
  inset: 0;
  z-index: 10000;
  display: grid;
  place-items: center;
  padding: max(24px, 1.6667vw);
  background: rgba(8, 17, 29, 0.76);
  backdrop-filter: blur(max(8px, 0.5556vw));
}

.gallery-dialog {
  width: min(94vw, max(960px, 66.6667vw));
  max-height: 92vh;
  overflow: auto;
  border-radius: max(18px, 1.25vw);
  background: #ffffff;
  box-shadow: 0 max(30px, 2.0833vw) max(90px, 6.25vw) rgba(0, 0, 0, 0.35);
}

.gallery-header {
  display: flex;
  align-items: center;
  justify-content: space-between;
  gap: max(20px, 1.3889vw);
  padding: max(18px, 1.25vw) max(22px, 1.5278vw);
  border-bottom: max(1px, 0.0694vw) solid #e4e9ef;
}

.gallery-header span {
  color: #2476c8;
  font-size: max(9px, 0.625vw);
  font-weight: 700;
  letter-spacing: max(1.3px, 0.0903vw);
  text-transform: uppercase;
}

.gallery-header h2 {
  margin: max(4px, 0.2778vw) 0 0;
  color: #163e73;
  font-size: max(22px, 1.5278vw);
}

.gallery-header button {
  width: max(40px, 2.7778vw);
  height: max(40px, 2.7778vw);
  display: grid;
  place-items: center;
  border: 0;
  border-radius: 50%;
  background: #f0f4f8;
  color: #4f5e6d;
  cursor: pointer;
  font-size: max(19px, 1.3194vw);
}

.gallery-stage {
  position: relative;
  height: min(62vh, max(590px, 40.9722vw));
  display: flex;
  align-items: center;
  justify-content: center;
  overflow: hidden;
  background: #eef2f6;
}

.gallery-stage > img {
  width: 100%;
  height: 100%;
  display: block;
  object-fit: contain;
}

.gallery-arrow {
  position: absolute;
  top: 50%;
  width: max(44px, 3.0556vw);
  height: max(44px, 3.0556vw);
  display: grid;
  place-items: center;
  border: 0;
  border-radius: 50%;
  background: rgba(255, 255, 255, 0.94);
  color: #173c6f;
  cursor: pointer;
  font-size: max(21px, 1.4583vw);
  transform: translateY(-50%);
  box-shadow: 0 max(7px, 0.4861vw) max(22px, 1.5278vw) rgba(0, 0, 0, 0.17);
}

.gallery-arrow-left {
  left: max(18px, 1.25vw);
}

.gallery-arrow-right {
  right: max(18px, 1.25vw);
}

.gallery-count {
  position: absolute;
  left: 50%;
  bottom: max(15px, 1.0417vw);
  padding: max(7px, 0.4861vw) max(12px, 0.8333vw);
  border-radius: max(999px, 69.375vw);
  background: rgba(10, 18, 28, 0.74);
  color: #ffffff;
  font-size: max(11px, 0.7639vw);
  transform: translateX(-50%);
}

.gallery-thumbnails {
  display: flex;
  justify-content: center;
  gap: max(9px, 0.625vw);
  padding: max(14px, 0.9722vw);
  overflow-x: auto;
}

.gallery-thumbnails button {
  width: max(75px, 5.2083vw);
  height: max(54px, 3.75vw);
  flex: 0 0 auto;
  overflow: hidden;
  padding: 0;
  border: max(2px, 0.1389vw) solid transparent;
  border-radius: max(8px, 0.5556vw);
  background: #e5eaf0;
  cursor: pointer;
  opacity: 0.58;
}

.gallery-thumbnails button.active {
  border-color: var(--accent);
  opacity: 1;
}

.gallery-thumbnails img {
  width: 100%;
  height: 100%;
  display: block;
  object-fit: cover;
}

.gallery-enter-active,
.gallery-leave-active {
  transition: opacity 0.22s ease;
}

.gallery-enter-from,
.gallery-leave-to {
  opacity: 0;
}

@media (max-width: 900px) {
  .hero-grid {
    grid-template-columns: 1fr;
  }

  .hero-image {
    height: max(460px, 31.9444vw);
  }

  .details-layout {
    grid-template-columns: 1fr;
  }

  .booking-card {
    position: static;
  }
}

@media (max-width: 600px) {
  .details-container {
    width: 90%;
  }

  .hero-image {
    height: max(330px, 22.9167vw);
    border-radius: max(15px, 1.0417vw);
  }

  .included-grid {
    grid-template-columns: 1fr;
  }

  .schedule-item {
    grid-template-columns: max(22px, 1.5278vw) max(65px, 4.5139vw) 1fr;
    gap: max(9px, 0.625vw);
  }

  .details-content {
    padding: max(55px, 3.8194vw) 0 max(70px, 4.8611vw);
  }

  .gallery-modal {
    padding: max(10px, 0.6944vw);
  }

  .gallery-stage {
    height: 56vh;
  }
}</style>