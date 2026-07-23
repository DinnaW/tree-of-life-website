<template>
  <div class="page">
    <SiteHeader @search="handleSearch" />

    <div class="container title-block">
      <h1>Tree of Life Nature Resort</h1>
      <div class="meta">
        <span class="rating">
          <svg class="star" viewBox="0 0 24 24"><path d="M12 2l2.9 6.6 7.1.6-5.4 4.7 1.7 7-6.3-3.9L5.7 21l1.7-7-5.4-4.7 7.1-.6z"/></svg>
          4.8
        </span>
        <span class="reviews">(256 reviews)</span>
        <span class="dot">·</span>
        <svg class="pin" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.6">
          <path d="M12 21s7-6.1 7-11.5A7 7 0 0 0 5 9.5C5 14.9 12 21 12 21z" />
          <circle cx="12" cy="9.5" r="2.3" />
        </svg>
        <span class="location">YAHALATENNA · KANDY, SRI LANKA</span>
      </div>
    </div>

    <PhotoGallery :photos="photos" @show-all="onShowAllPhotos" />

    <div class="container action-row">
      <button class="icon-btn" aria-label="Save">
        <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.6">
          <path d="M12 20.5s-7.5-4.6-10-9.3C.5 7.4 2.6 4 6.2 4c2 0 3.6 1 5.8 3.4C14.2 5 15.8 4 17.8 4c3.6 0 5.7 3.4 4.2 7.2-2.5 4.7-10 9.3-10 9.3z"/>
        </svg>
      </button>
      <button class="icon-btn" aria-label="Share">
        <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.6">
          <circle cx="18" cy="5" r="2.6" /><circle cx="6" cy="12" r="2.6" /><circle cx="18" cy="19" r="2.6" />
          <path d="M8.3 10.7l7.4-4.2M8.3 13.3l7.4 4.2" />
        </svg>
      </button>
    </div>

    <StickyNav ref="stickyNav" :sections="sections" />

    <!-- Overview -->
      <div class="container content">

        <section id="overview" class="content-section">
          <OverviewSection />
        </section>

      </div>

      <!-- FULL WIDTH -->
      <section id="amenities" class="content-section amenities-full">
        <AmenitiesSection />
      </section>

      <!-- Back inside container -->
      <div class="container content">

        <section id="rooms" class="content-section">
          <RoomsSection />
        </section>

        <section id="guest-reviews" class="content-section">
          <GuestReviews />
        </section>

        <section id="policies" class="content-section">
          <PoliciesSection />
        </section>

        <section id="faq-section" class="content-section">
          <FaqSection/>
        </section>

      </div>

    <FooterSection/>

    <!-- Scroll To Top Button -->
    <button
      v-if="showScrollButton"
      class="scroll-top-btn"
      @click="scrollToTop"
      aria-label="Scroll to top"
    >
      <svg viewBox="0 0 24 24">
        <path d="M12 19V5" />
        <path d="M5 12l7-7 7 7" />
      </svg>
    </button>
    
  </div>
</template>

<script setup>
import { ref, onMounted, onUnmounted } from "vue";
import SiteHeader from "./components/SiteHeader.vue";
import PhotoGallery from "./components/PhotoGallery.vue";
import StickyNav from "./components/StickyNav.vue";
import OverviewSection from "./components/OverviewSection.vue"
import AmenitiesSection from "./components/AmenitiesSection.vue";
import RoomsSection from "./components/RoomsSection.vue";
import GuestReviews from "./components/GuestReviews.vue";
import PoliciesSection from "./components/PoliciesSection.vue";
import FooterSection from './components/FooterSection.vue'
import AvailabilityBar from "./components/AvailabilityBar.vue";
import FaqSection from "./components/FaqSection.vue";


const stickyNav = ref(null);

// Swap these src URLs for your real photos
const baseUrl = import.meta.env.BASE_URL;

const photos = [
  { src: `${baseUrl}images/gallery1.jpg`, alt: "Pool aerial view" },
  { src: `${baseUrl}images/gallery2.jpg`, alt: "Sunset over mountains" },
  { src: `${baseUrl}images/gallery3.jpg`, alt: "Bedroom" },
  { src: `${baseUrl}images/gallery4.jpg`, alt: "Deck pool" },
  { src: `${baseUrl}images/gallery5.jpg`, alt: "Villa exterior" },
];

const sections = [
  { id: "overview", label: "OVERVIEW" },
  { id: "amenities", label: "AMENITIES" },
  { id: "rooms", label: "ROOMS" },
  { id: "guest-reviews", label: "GUEST REVIEWS" },
  { id: "policies", label: "POLICIES" },
  { id: "faq-section", label: "FAQ" },
];

function onShowAllPhotos() {
  console.log("Open full photo gallery");
}

function handleSearch(criteria) {
  console.log("Searching with:", criteria);

  stickyNav.value?.scrollToSection("rooms");
}

const showScrollButton = ref(false);

function handleScroll() {
  const scrollPosition = window.scrollY;
  const pageHeight = document.documentElement.scrollHeight;
  const windowHeight = window.innerHeight;

  // Show only near the bottom of the page
  showScrollButton.value =
    scrollPosition + windowHeight >= pageHeight - 250;
}

function scrollToTop() {
  window.scrollTo({
    top: 0,
    behavior: "smooth"
  });
}


onMounted(() => {
  window.addEventListener("scroll", handleScroll);
});

onUnmounted(() => {
  window.removeEventListener("scroll", handleScroll);
});
</script>

<style scoped>

.container {
  max-width: 1340px;
  margin: 0 auto;
  padding: 0 34px;
}

.title-block {
  margin-top: 70px;
}

.title-block h1 {
  color: #1A51AD;
  font-size: 39px;
  font-weight: 600;
  margin: 0 0 10px;
}
.meta {
  display: flex;
  align-items: center;
  gap: 8px;
  font-size: 14px;
  color: #555;
}
.rating {
  display: inline-flex;
  align-items: center;
  margin-bottom: 40px;
  gap: 5px;
  color: #1a1a1a;
  font-weight: 700;
}
.star {
  width: 15px;
  height: 15px;
  fill: #f5c347;
}
.reviews {
  color: #6b6b6b;
  margin-bottom: 40px;
}

.dot {
  color: #ccc;
  margin-bottom: 40px;

}
.pin {
  width: 15px;
  height: 15px;
  color: #6b6b6b;
  margin-bottom: 40px;
}
.location {
  letter-spacing: 0.4px;
  color: #6b6b6b;
  font-size: 12.5px;
  font-weight: 300;
  margin-bottom: 40px;
}
.action-row {
  display: flex;
  justify-content: flex-end;
  align-items: center;
  gap: 12px;
  margin-top: 18px;
}
.icon-btn {
  background: none;
  border: 1px solid #dcdcdc;
  border-radius: 6px;
  width: 42px;
  height: 42px;
  display: inline-flex;
  align-items: center;
  justify-content: center;
  cursor: pointer;
  color: #2d6adc;
}
.icon-btn svg {
  width: 19px;
  height: 19px;
}

.content {
  padding-top: 36px;
  padding-bottom: 45px;
}
.content-section {
  min-height: 260px;
  padding: 36px 0;
  border-bottom: 1px solid #f0f0f0;
  color: #444;
  line-height: 1.7;
  font-size: 15px;
}
.eyebrow {
  color: #2d6adc;
  font-weight: 700;
  letter-spacing: 1px;
  font-size: 13px;
  margin-bottom: 18px;
}
.amenities-list {
  padding-left: 20px;
  margin: 0;
}

.scroll-top-btn {
  position: fixed;
  right: 35px;
  bottom: 80px;

  width: 52px;
  height: 52px;

  border-radius: 50%;
  border: none;

  background: #5187e4;
  color: white;

  display: flex;
  align-items: center;
  justify-content: center;

  cursor: pointer;

  box-shadow: 0 8px 25px rgba(26,81,173,0.35);

  transition: all 0.3s ease;

  z-index: 999;
}


.scroll-top-btn:hover {
  background: #2d6adc;
  transform: translateY(-5px);
}


.scroll-top-btn svg {
  width: 22px;
  height: 22px;

  fill: none;
  stroke: currentColor;
  stroke-width: 2;
  stroke-linecap: round;
  stroke-linejoin: round;
}
</style>
