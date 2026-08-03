<template>
  <div class="page">
    <!-- Header -->
    <div
  class="site-header-wrapper"
  :class="{
    'package-header':
      currentPage === 'packages' ||
      currentPage === 'package-details' ||
      currentPage === 'package-checkout' ||
      currentPage === 'checkout' ||
      currentPage === 'confirmed'
  }"
>
  <SiteHeader
    v-model:booking-type="bookingType"
    @search="handleSearch"
    @show-home="showHomePage"
    @show-packages="showPackagesPage"
  />
</div>

  <main v-if="currentPage === 'home'">
      <div class="container title-block">
        <h1>Tree of Life Nature Resort</h1>

        <div class="meta">
          <span class="rating">
            <svg class="star" viewBox="0 0 24 24">
              <path
                d="M12 2l2.9 6.6 7.1.6-5.4 4.7 1.7 7-6.3-3.9L5.7 21l1.7-7-5.4-4.7 7.1-.6z"
              />
            </svg>
            4.8
          </span>

          <span class="reviews">(256 reviews)</span>
          <span class="dot">·</span>

          <svg
            class="pin"
            viewBox="0 0 24 24"
            fill="none"
            stroke="currentColor"
            stroke-width="1.6"
          >
            <path
              d="M12 21s7-6.1 7-11.5A7 7 0 0 0 5 9.5C5 14.9 12 21 12 21z"
            />
            <circle cx="12" cy="9.5" r="2.3" />
          </svg>

          <span class="location">YAHALATENNA · KANDY, SRI LANKA</span>
        </div>
      </div>

      <PhotoGallery :photos="photos" @show-all="onShowAllPhotos" />

      <div class="container action-row">
        <button type="button" class="icon-btn" aria-label="Save">
          <svg
            viewBox="0 0 24 24"
            fill="none"
            stroke="currentColor"
            stroke-width="1.6"
          >
            <path
              d="M12 20.5s-7.5-4.6-10-9.3C.5 7.4 2.6 4 6.2 4c2 0 3.6 1 5.8 3.4C14.2 5 15.8 4 17.8 4c3.6 0 5.7 3.4 4.2 7.2-2.5 4.7-10 9.3-10 9.3z"
            />
          </svg>
        </button>

        <button
          type="button"
          class="icon-btn"
          aria-label="Share"
          @click="showShareModal = true"
        >
          <svg
            viewBox="0 0 24 24"
            fill="none"
            stroke="currentColor"
            stroke-width="1.6"
          >
            <circle cx="18" cy="5" r="2.6" />
            <circle cx="6" cy="12" r="2.6" />
            <circle cx="18" cy="19" r="2.6" />
            <path d="M8.3 10.7l7.4-4.2M8.3 13.3l7.4 4.2" />
          </svg>
        </button>
      </div>

      <ShareModal
        :show="showShareModal"
        title="Tree of Life Nature Resort"
        :image="photos[0]?.src"
        @close="showShareModal = false"
      />

     <StickyNav
      v-if="currentPage === 'home'"
      ref="stickyNav"
      :sections="sections"
      />

      <div class="container content">
        <section id="overview" class="content-section">
          <OverviewSection />
        </section>
      </div>

      <section id="amenities" class="content-section amenities-full">
        <AmenitiesSection />
      </section>

      <div class="container content">
        <section
          id="rooms"
          class="content-section rooms-packages-section"
        >
          <div class="results-switch-row">
            <div class="browse-control">
              <span class="browse-label">BROWSE BY</span>

              <div class="browse-switch">
                <button
                  type="button"
                  :class="{ active: bookingType === 'rooms' }"
                  @click="setBookingType('rooms')"
                >
                  Rooms
                </button>

                <button
                  type="button"
                  :class="{ active: bookingType === 'packages' }"
                  @click="setBookingType('packages')"
                >
                  Packages
                </button>
              </div>
            </div>
          </div>

          <RoomsSection
            v-if="bookingType === 'rooms'"
            @reserve="reserveRoom"
          />

          <PackageResults
            v-else
            @checkout-package="bookPackage"
          />
        </section>

        <section id="guest-reviews" class="content-section">
          <GuestReviews />
        </section>

        <section id="policies" class="content-section">
          <PoliciesSection />
        </section>

        <section id="faq-section" class="content-section">
          <FaqSection />
        </section>
      </div>
    </main>

    <main
      v-if="currentPage === 'packages'"
      class="packages-page"
    >
      <PackagesSection @view-package="bookPackage" />
    </main>

    <PackageDetails
      v-if="currentPage === 'package-details' && selectedPackage"
      :pkg="selectedPackage"
      @back="showPackagesPage"
      @book="bookPackage"
    />

    <CheckoutPage
      v-else-if="currentPage === 'checkout'"
      :room="checkoutRoom"
      :nights="checkoutNights"
      @back="backToHome"
      @remove="handleRemoveRoom"
      @confirmed="handleConfirmed"
      @go-to-section="goToHomeSection"
    />

    <ConfirmationPage
      v-else-if="currentPage === 'confirmed'"
      :room="confirmation.room"
      :nights="confirmation.nights"
      :guest="confirmation.guest"
      :total="confirmation.total"
      :booking-ref="confirmation.bookingRef"
      @back="backToHome"
      @go-to-section="goToSectionFromConfirmation"
    />

    <FooterSection
  v-if="
    !reservationSuccess &&
    currentPage !== 'package-checkout' &&
    currentPage !== 'room-checkout'
  "
    />

    <button
      v-if="showScrollButton && !reservationSuccess"
      type="button"
      class="scroll-top-btn"
      aria-label="Scroll to top"
      @click="scrollToTop"
    >
      <svg viewBox="0 0 24 24">
        <path d="M12 19V5" />
        <path d="M5 12l7-7 7 7" />
      </svg>
    </button>
  </div>
</template>

<script setup>
const checkoutRoom = ref(null);
const checkoutNights = ref(1);

const confirmation = ref({
  room: null,
  nights: 1,
  guest: null,
  total: 0,
  bookingRef: ""
});

import {
  ref,
  nextTick,
  onMounted,
  onUnmounted
} from "vue";

import SiteHeader from "./components/SiteHeader.vue";
import PhotoGallery from "./components/PhotoGallery.vue";
import StickyNav from "./components/StickyNav.vue";
import OverviewSection from "./components/OverviewSection.vue";
import AmenitiesSection from "./components/AmenitiesSection.vue";
import RoomsSection from "./components/RoomsSection.vue";
import GuestReviews from "./components/GuestReviews.vue";
import PoliciesSection from "./components/PoliciesSection.vue";
import FaqSection from "./components/FaqSection.vue";
import ConfirmationPage from "./components/ConfirmationPage.vue";
import CheckoutPage from "./components/CheckoutPage.vue";
import ShareModal from "./components/modals/ShareModal.vue";
import PackageResults from "./components/PackageResults.vue";
import PackagesSection from "./components/PackagesSection.vue";
import PackageDetails from "./components/PackageDetails.vue";
import FooterSection from "./components/FooterSection.vue";


const currentPage = ref("home");
const stickyNav = ref(null);
const showShareModal = ref(false);
const bookingType = ref("rooms");
const selectedPackage = ref(null);
const selectedRoom = ref(null);
const showScrollButton = ref(false);

const baseUrl = import.meta.env.BASE_URL;

const photos = [
  {
    src: `${baseUrl}images/gallery1.jpg`,
    alt: "Pool aerial view"
  },
  {
    src: `${baseUrl}images/gallery2.jpg`,
    alt: "Sunset over mountains"
  },
  {
    src: `${baseUrl}images/gallery3.jpg`,
    alt: "Bedroom"
  },
  {
    src: `${baseUrl}images/gallery4.jpg`,
    alt: "Deck pool"
  },
  {
    src: `${baseUrl}images/gallery5.jpg`,
    alt: "Villa exterior"
  }
];

const sections = [
  { id: "overview", label: "OVERVIEW" },
  { id: "amenities", label: "AMENITIES" },
  { id: "rooms", label: "ROOMS & PACKAGES" },
  { id: "guest-reviews", label: "GUEST REVIEWS" },
  { id: "policies", label: "POLICIES" },
  { id: "faq-section", label: "FAQ" }
];

function onShowAllPhotos() {
  console.log("Open full photo gallery");
}

async function setBookingType(type) {
  bookingType.value = type;

  await nextTick();

  stickyNav.value?.scrollToSection("rooms");
}

async function showHomePage() {
  showShareModal.value = false;
  reservationSuccess.value = false;
  reservationData.value = null;
  selectedPackage.value = null;
  currentPage.value = "home";

  await nextTick();

  window.scrollTo({
    top: 0,
    behavior: "smooth"
  });
}

async function showPackagesPage() {
  showShareModal.value = false;
  reservationSuccess.value = false;
  reservationData.value = null;
  selectedPackage.value = null;
  currentPage.value = "packages";

  await nextTick();

  window.scrollTo({
    top: 0,
    behavior: "smooth"
  });
}

async function showPackageDetails(pkg) {
  showShareModal.value = false;
  selectedPackage.value = pkg;
  currentPage.value = "package-details";

  await nextTick();

  window.scrollTo({
    top: 0,
    behavior: "smooth"
  });
}

async function bookPackage(pkg) {
  showShareModal.value = false;
  selectedPackage.value = pkg;

  checkoutRoom.value = {
    ...pkg,

    // CheckoutPage expects room-style property names
    name: pkg.title,
    tag: pkg.category,
    image: pkg.image,

    // Package information
    bed: "Package stay",
    meal: pkg.includes?.join(" · ") || "Package inclusions",

    roomCount: 1,
    extraBeds: 0,

    cancellation:
      pkg.cancellation ||
      "Free cancellation up to 48 hours before arrival",

    isPackage: true
  };

  checkoutNights.value = parseInt(pkg.stay) || 1;
  currentPage.value = "checkout";

  await nextTick();

  window.scrollTo({
    top: 0,
    behavior: "smooth"
  });
}

async function reserveRoom(payload) {
  showShareModal.value = false;

  checkoutRoom.value = payload.room;
  checkoutNights.value = payload.nights || 1;

  currentPage.value = "checkout";

  await nextTick();

  window.scrollTo({
    top: 0,
    behavior: "smooth"
  });
}

async function removePackage() {
  selectedPackage.value = null;
  currentPage.value = "packages";

  await nextTick();

  window.scrollTo({
    top: 0,
    behavior: "smooth"
  });
}
async function removeRoom() {
  selectedRoom.value = null;
  currentPage.value = "home";
  bookingType.value = "rooms";

  await nextTick();

  stickyNav.value?.scrollToSection("rooms");
}

const reservationSuccess = ref(false);
const reservationData = ref(null);

async function handleReservationConfirmed(reservation) {
  reservationData.value = reservation;
  reservationSuccess.value = true;

  await nextTick();
}
async function handleRoomReservationConfirmed(reservation) {
  reservationData.value = reservation;
  reservationSuccess.value = true;

  await nextTick();
}

async function handleSearch(criteria) {
  console.log("Searching with:", criteria);

  if (criteria?.bookingType === "packages") {
    bookingType.value = "packages";
  } else if (criteria?.bookingType === "rooms") {
    bookingType.value = "rooms";
  }

  if (currentPage.value !== "home") {
    currentPage.value = "home";
    await nextTick();
  }

  stickyNav.value?.scrollToSection("rooms");
}

function handleScroll() {
  const scrollPosition = window.scrollY;
  const pageHeight = document.documentElement.scrollHeight;
  const windowHeight = window.innerHeight;

  showScrollButton.value =
    scrollPosition + windowHeight >= pageHeight - 250;
}

function scrollToTop() {
  window.scrollTo({
    top: 0,
    behavior: "smooth"
  });
}

async function handleConfirmed(reservation) {
  const bookingRef =
    "TOL-" +
    Date.now().toString().slice(-6);

  confirmation.value = {
    room: reservation.room,
    nights: checkoutNights.value,
    guest: reservation.guest,
    total: reservation.total,
    bookingRef: bookingRef
  };

  currentPage.value = "confirmed";

  await nextTick();

  window.scrollTo({
    top: 0,
    behavior: "smooth"
  });
}

async function backToHome() {
  checkoutRoom.value = null;
  checkoutNights.value = 1;

  currentPage.value = "home";
  bookingType.value = "rooms";

  await nextTick();

  window.scrollTo({
    top: 0,
    behavior: "smooth"
  });
}

async function goToHomeSection(sectionId) {
  bookingType.value = "rooms";
  currentPage.value = "home";

  await nextTick();

  setTimeout(() => {
    const target = document.getElementById(sectionId);

    if (target) {
      target.scrollIntoView({
        behavior: "smooth",
        block: "start"
      });
    }
  }, 100);
}

async function goToSectionFromConfirmation(sectionId) {
  currentPage.value = "home";

  await nextTick();

  stickyNav.value?.scrollToSection(sectionId);
}

onMounted(() => {
  window.addEventListener("scroll", handleScroll, {
    passive: true
  });

  handleScroll();
});

async function handleRemoveRoom() {
  checkoutRoom.value = null;
  checkoutNights.value = 1;

  // Stay on checkout page
  currentPage.value = "checkout";

  await nextTick();

  window.scrollTo({
    top: 0,
    behavior: "smooth"
  });
}

onUnmounted(() => {
  window.removeEventListener("scroll", handleScroll);
});
</script>

<style scoped>

.container {
  max-width: max(1340px, 93.0556vw);
  margin: 0 auto;
  padding: 0 max(34px, 2.3611vw);
}

.title-block {
  margin-top: max(70px, 4.8611vw);
}

.title-block h1 {
  color: #1A51AD;
  font-size: max(39px, 2.7083vw);
  font-weight: 550;
  margin: 0 0 max(10px, 0.6944vw);
}
.meta {
  display: flex;
  align-items: center;
  gap: max(8px, 0.5556vw);
  font-size: max(14px, 0.9722vw);
  color: #555;
}
.rating {
  display: inline-flex;
  align-items: center;
  margin-bottom: max(40px, 2.7778vw);
  gap: max(5px, 0.3472vw);
  color: #1a1a1a;
  font-weight: 700;
}
.star {
  width: max(15px, 1.0417vw);
  height: max(15px, 1.0417vw);
  fill: #f5c347;
}
.reviews {
  color: #6b6b6b;
  margin-bottom: max(40px, 2.7778vw);
}

.dot {
  color: #ccc;
  margin-bottom: max(40px, 2.7778vw);

}
.pin {
  width: max(15px, 1.0417vw);
  height: max(15px, 1.0417vw);
  color: #6b6b6b;
  margin-bottom: max(40px, 2.7778vw);
}
.location {
  letter-spacing: max(0.4px, 0.0278vw);
  color: #6b6b6b;
  font-size: max(12.5px, 0.8681vw);
  font-weight: 300;
  margin-bottom: max(40px, 2.7778vw);
}
.action-row {
  display: flex;
  justify-content: flex-end;
  align-items: center;
  gap: max(12px, 0.8333vw);
  margin-top: max(18px, 1.25vw);
}

.icon-btn {
  width: max(42px, 2.9167vw);
  height: max(42px, 2.9167vw);

  display: flex;
  align-items: center;
  justify-content: center;

  border: max(1px, 0.0694vw) solid #d8dde6;
  border-radius: max(8px, 0.5556vw);

  background: #ffffff;
  color: #0b5cab;

  cursor: pointer;

  transition:
    transform 0.25s ease,
    background-color 0.25s ease,
    color 0.25s ease,
    border-color 0.25s ease,
    box-shadow 0.25s ease;
}

.icon-btn svg {
  width: max(18px, 1.25vw);
  height: max(18px, 1.25vw);

  transition:
    transform 0.25s ease,
    stroke 0.25s ease;
}

.icon-btn:hover {
  border-color: #0b5cab;

  transform: translateY(max(-2px, -0.1389vw));

  box-shadow:
    0 max(8px, 0.5556vw)
    max(20px, 1.3889vw)
    rgba(11, 92, 171, 0.22);
}

.icon-btn:hover svg {
  transform: scale(1.08);
}

.icon-btn:active {
  transform: scale(0.96);
}

.icon-btn:focus-visible {
  outline: none;
  border-color: #0b5cab;
  box-shadow:
    0 0 0 max(3px, 0.2083vw)
    rgba(11, 92, 171, 0.18);
}

.content {
  padding-top: max(36px, 2.5vw);
  padding-bottom: max(45px, 3.125vw);
}
.content-section {
  min-height: max(260px, 18.0556vw);
  padding: max(36px, 2.5vw) 0;
  border-bottom: max(1px, 0.0694vw) solid #f0f0f0;
  color: #444;
  line-height: 1.7;
  font-size: max(15px, 1.0417vw);
}
.eyebrow {
  color: #2d6adc;
  font-weight: 700;
  letter-spacing: max(1px, 0.0694vw);
  font-size: max(13px, 0.9028vw);
  margin-bottom: max(18px, 1.25vw);
}
.amenities-list {
  padding-left: max(20px, 1.3889vw);
  margin: 0;
}

.scroll-top-btn {
  position: fixed;
  right: max(35px, 2.4306vw);
  bottom: max(80px, 5.5556vw);

  width: max(52px, 3.6111vw);
  height: max(52px, 3.6111vw);

  border-radius: 50%;
  border: none;

  background: #5187e4;
  color: white;

  display: flex;
  align-items: center;
  justify-content: center;

  cursor: pointer;

  box-shadow: 0 max(8px, 0.5556vw) max(25px, 1.7361vw) rgba(26,81,173,0.35);

  transition: all 0.3s ease;

  z-index: 999;
}


.scroll-top-btn:hover {
  background: #2d6adc;
  transform: translateY(min(-5px, -0.3472vw));
}


.scroll-top-btn svg {
  width: max(22px, 1.5278vw);
  height: max(22px, 1.5278vw);

  fill: none;
  stroke: currentColor;
  stroke-width: 2;
  stroke-linecap: round;
  stroke-linejoin: round;
}


/* NEW PAGE AND PACKAGE STYLES */

.page {
  width: 100%;
  min-height: 100vh;
  overflow-x: hidden;
}

.site-header-wrapper {
  width: 100%;
}

.package-header :deep(.search-bar) {
  display: none !important;
}

.package-header :deep(.header) {
  min-height: max(100px, 6.9444vw) !important;
}

.package-header :deep(.header-inner) {
  min-height: max(30px, 2.0833vw) !important;
  display: flex;
  align-items: center;
}

.popup-overlay {
  position: fixed;
  inset: 0;
  z-index: 99999;
  display: grid;
  place-items: center;
  padding: max(20px, 1.3889vw);
  background: rgba(7, 24, 49, 0.58);
  backdrop-filter: blur(max(5px, 0.3472vw));
}

.popup-card {
  width: min(100%, max(360px, 25vw));
  padding:
    max(30px, 2.0833vw)
    max(26px, 1.8056vw)
    max(26px, 1.8056vw);
  border: max(1px, 0.0694vw) solid rgba(255, 255, 255, 0.7);
  border-radius: max(20px, 1.3889vw);
  background: #ffffff;
  text-align: center;
  box-shadow:
    0 max(24px, 1.6667vw)
    max(70px, 4.8611vw)
    rgba(7, 24, 49, 0.24);
}

.popup-icon {
  width: max(62px, 4.3056vw);
  height: max(62px, 4.3056vw);
  display: grid;
  place-items: center;
  margin: 0 auto max(16px, 1.1111vw);
  border: max(7px, 0.4861vw) solid #e7f7ee;
  border-radius: 50%;
  background: #168553;
}

.popup-icon svg {
  width: max(29px, 2.0139vw);
  height: max(29px, 2.0139vw);
  fill: none;
  stroke: #ffffff;
  stroke-width: 2.5;
  stroke-linecap: round;
  stroke-linejoin: round;
}

.popup-eyebrow {
  margin: 0 0 max(7px, 0.4861vw);
  color: #168553;
  font-size: max(11px, 0.7639vw);
  font-weight: 800;
  letter-spacing: 0.14em;
  text-transform: uppercase;
}

.popup-card h2 {
  margin: 0;
  color: #123f79;
  font-size: max(30px, 2.0833vw);
  line-height: 1.1;
}

.popup-message,
.popup-email {
  margin: max(13px, 0.9028vw) 0 0;
  color: #697689;
  font-size: max(14px, 0.9722vw);
  line-height: 1.65;
}

.popup-message strong,
.popup-email strong {
  color: #273b54;
}

.popup-button {
  width: 100%;
  min-height: max(45px, 3.125vw);
  margin-top: max(22px, 1.5278vw);
  padding: 0 max(20px, 1.3889vw);
  border: 0;
  border-radius: max(10px, 0.6944vw);
  background: #174d91;
  color: #ffffff;
  font: inherit;
  font-size: max(14px, 0.9722vw);
  font-weight: 700;
  cursor: pointer;
  transition: background 0.2s ease, transform 0.2s ease;
}

.popup-button:hover {
  background: #103d76;
  transform: translateY(max(-1px, -0.0694vw));
}

.popup-enter-active,
.popup-leave-active {
  transition: opacity 0.22s ease;
}

.popup-enter-active .popup-card,
.popup-leave-active .popup-card {
  transition: transform 0.22s ease, opacity 0.22s ease;
}

.popup-enter-from,
.popup-leave-to {
  opacity: 0;
}

.popup-enter-from .popup-card,
.popup-leave-to .popup-card {
  opacity: 0;
  transform: translateY(max(14px, 0.9722vw)) scale(0.96);
}

.rooms-packages-section {
  position: relative;
  scroll-margin-top: max(86px, 5.9722vw);
}

.results-switch-row {
  position: relative;
  z-index: 20;
  display: flex;
  justify-content: flex-end;
  margin-bottom: min(-78px, -5.4167vw);
  padding:
    max(24px, 1.6667vw)
    max(30px, 2.0833vw)
    0;
  pointer-events: none;
}

.browse-control {
  position: absolute;
  top: min(-20px, -1.3889vw);
  right: max(40px, 2.7778vw);
  width: max(268px, 18.6111vw);
  pointer-events: auto;
}

.browse-label {
  display: block;
  margin-bottom: max(8px, 0.5556vw);
  color: #667085;
  font-size: max(10px, 0.6944vw);
  font-weight: 700;
  letter-spacing: 0.08em;
}

.browse-switch {
  display: grid;
  grid-template-columns: repeat(2, minmax(0, 1fr));
  gap: max(4px, 0.2778vw);
  width: 100%;
  padding: max(4px, 0.2778vw);
  border: max(1px, 0.0694vw) solid #d9e3ef;
  border-radius: max(12px, 0.8333vw);
  background: #eaf1f9;
}

.browse-switch button {
  min-height: max(42px, 2.9167vw);
  padding: 0 max(18px, 1.25vw);
  border: 0;
  border-radius: max(9px, 0.625vw);
  background: transparent;
  color: #657286;
  font: inherit;
  font-size: max(12px, 0.8333vw);
  font-weight: 700;
  cursor: pointer;
  transition:
    background 0.2s ease,
    color 0.2s ease,
    box-shadow 0.2s ease;
}

.browse-switch button:hover {
  color: #1a51ad;
}

.browse-switch button.active {
  background: #ffffff;
  color: #1a51ad;
  box-shadow:
    0 max(4px, 0.2778vw)
    max(14px, 0.9722vw)
    rgba(29, 72, 126, 0.1);
}

.packages-page {
  width: 100%;
  min-height: 100vh;
}

@media (max-width: 900px) {
  .results-switch-row {
    margin-bottom: max(20px, 1.3889vw);
    padding: 0;
  }

  .browse-control {
    position: relative;
    top: auto;
    right: auto;
    width: 100%;
  }
}

@media (max-width: 768px) {
  .package-header :deep(.header),
  .package-header :deep(.header-inner) {
    min-height: 110px !important;
  }

  .popup-card {
    padding: 26px 20px 22px;
    border-radius: 17px;
  }

  .popup-card h2 {
    font-size: 27px;
  }
}

@media (max-width: 560px) {
  .browse-switch button {
    min-height: 40px;
    padding: 0 12px;
  }
}

</style>
