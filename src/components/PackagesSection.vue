<template>
  <main class="packages-page">
    
<section class="packages-hero">
  <div class="hero-wave hero-wave-top"></div>
  <div class="hero-wave hero-wave-middle"></div>
  <div class="hero-wave hero-wave-bottom"></div>

  <div class="page-container">
    <div class="hero-content">
      <div class="eyebrow"> — PACKAGES </div>
      <h1>
        Discover Our Premium Packages
      </h1>

      <p>
        Curated experiences designed to make your stay unforgettable.
      </p>
    </div>
  </div>
</section>

    <section id="packages-list" class="packages-section">
      <div class="page-container">
        <header class="section-heading">

        </header>

        <div class="packages-grid">
          <article
            v-for="pkg in packages"
            :key="pkg.id"
            class="package-card"
          >
            <button
              type="button"
              class="package-image"
              :aria-label="`Open ${pkg.title} gallery`"
              @click="openGallery(pkg)"
            >
              <img :src="pkg.image" :alt="pkg.title" />

              <span class="package-image-shade"></span>

              <span class="package-category">
                {{ pkg.category }}
              </span>

             <span class="gallery-hover">
    <span class="gallery-hover-icon">
        <i class="fa-solid fa-images"></i>
    </span>

    <strong>View Gallery</strong>

                  <small>{{ pkg.gallery.length }} photos</small>
                </span>
         
            </button>

            <div class="package-content">
              <div class="package-heading">
                <div>
                  <h3>{{ pkg.title }}</h3>
                  <p>{{ pkg.description }}</p>
                </div>

                <span class="package-rating">
                  <i class="fa-solid fa-star"></i>
                  {{ pkg.rating }}
                </span>
              </div>

              <div class="package-meta">
                <span>
                  <i class="fa-solid fa-moon"></i>
                  {{ pkg.stay }}
                </span>

                <span>
                  <i class="fa-solid fa-user-group"></i>
                  {{ pkg.guests }}
                </span>

                <span>
                  <i class="fa-solid fa-bed"></i>
                  {{ pkg.room }}
                </span>
              </div>

              <ul class="package-includes">
                <li v-for="item in pkg.includes" :key="item">
                  <i class="fa-solid fa-check"></i>
                  {{ item }}
                </li>
              </ul>

              <div class="package-footer">
                <div class="package-price">
                  <small>Starting from</small>
                  <strong>${{ pkg.price }}</strong>
                  <span>per package</span>
                </div>

                <button
  type="button"
  class="package-button"
  @click="selectPackage(pkg)"
>
  Purchase Package
</button>
              </div>
            </div>
          </article>
        </div>
      </div>
    </section>

  <!-- SIMPLE BENEFITS -->
    <section class="benefits-section">
      <div class="page-container">
        <div class="benefits-grid">
          <article
            v-for="benefit in benefits"
            :key="benefit.title"
            class="benefit-item"
          >
            <div class="benefit-icon">
              <i :class="benefit.icon"></i>
            </div>

            <div>
              <h3>{{ benefit.title }}</h3>
              <p>{{ benefit.description }}</p>
            </div>
          </article>
        </div>
      </div>
    </section>
    

    <!-- CTA -->
    <section class="cta-section">
      <div class="page-container">
        <div class="cta-box">
          <div>
            <p class="section-label">Plan your stay</p>
            <h2>Ready for a peaceful escape?</h2>
            <p class="cta-description">
  Choose your preferred package and begin planning your stay.
</p>
          </div>

          <div class="cta-actions">
            <a href="#packages-list" class="cta-primary">
              View Packages
            </a>

            <a href="tel:+94000000000" class="cta-secondary">
              <i class="fa-solid fa-phone"></i>
              Contact Resort
            </a>
          </div>
        </div>
      </div>
    </section>
    <PackageGalleryModal
      :show="galleryOpen"
      :package-data="activePackage"
      @close="closeGallery"
    />
  </main>
</template>

<script setup>

import { ref } from "vue";

import PackageGalleryModal from "./modals/PackageGalleryModal.vue";

const emit = defineEmits(["view-package"]);
const baseUrl = import.meta.env.BASE_URL;

const packages = [
  {
    id: 1,
    category: "Nature Escape",
    title: "Signature Nature Stay",
    subtitle: "A peaceful escape surrounded by tropical nature.",
    description:
      "A peaceful stay with breakfast, a guided nature walk and private dining.",

    longDescription:
      "Reconnect with nature through a carefully planned resort experience. Enjoy comfortable accommodation, fresh breakfast, a guided walk through the surrounding landscape and a private dining experience prepared for two.",

    image: `${baseUrl}images/img1.png`,

    gallery: [   
      "https://images.unsplash.com/photo-1776761420449-48c332b4a555?q=80&w=2070&auto=format&fit=crop&ixlib=rb-4.1.0&ixid=M3wxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8fA%3D%3D",
      "https://images.unsplash.com/photo-1504754524776-8f4f37790ca0?q=80&w=2070&auto=format&fit=crop&ixlib=rb-4.1.0&ixid=M3wxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8fA%3D%3D",
      "https://images.unsplash.com/photo-1770563182638-6294f5851a41?q=80&w=2499&auto=format&fit=crop&ixlib=rb-4.1.0&ixid=M3wxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8fA%3D%3D"   
    ],

    rating: "4.5",
    stay: "1 night",
    guests: "2 guests",
    room: "Luxury Deluxe",
    price: 150,

    includes: [
      "Breakfast for two",
      "Guided nature walk",
      "Private dinner"
    ],

    highlights: [
      "Welcome drink on arrival",
      "One-night Luxury Deluxe accommodation",
      "Daily breakfast for two guests",
      "Guided nature walk",
      "Private dinner experience",
      "Complimentary Wi-Fi",
      "Free parking"
    ],

    schedule: [
      {
        time: "2:00 PM",
        title: "Arrival and check-in",
        text: "Welcome drink followed by check-in to your Luxury Deluxe room."
      },
      {
        time: "4:00 PM",
        title: "Guided nature walk",
        text: "Explore the resort surroundings with a local guide."
      },
      {
        time: "7:30 PM",
        title: "Private dinner",
        text: "Enjoy a peaceful private dining experience for two."
      },
      {
        time: "8:00 AM",
        title: "Breakfast",
        text: "Begin the morning with a freshly prepared breakfast."
      }
    ],

    policies: [
      "Free cancellation up to 48 hours before arrival",
      "Check-in from 2:00 PM",
      "Check-out before 11:00 AM",
      "Package price applies to two guests"
    ]
  },

  {
  id: 2,
  category: "Farm Experience",
  title: "Strawberry Harvest & Stay",
  subtitle: "Enjoy a refreshing hillside stay and hand-pick fresh strawberries.",
  description:
    "A relaxing stay with a guided strawberry-picking experience and farm-fresh tasting.",

  longDescription:
    "Escape to the cool hillside countryside and enjoy a memorable strawberry harvest experience. Walk through a local strawberry farm, learn how the fruit is grown, hand-pick ripe strawberries and enjoy a fresh tasting before returning to your peaceful resort stay.",

  image: `${baseUrl}images/img2.png`,

  gallery: [
      "https://images.unsplash.com/photo-1759509280455-caaf50c32cea?q=80&w=3131&auto=format&fit=crop&ixlib=rb-4.1.0&ixid=M3wxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8fA%3D%3D",
      "https://plus.unsplash.com/premium_photo-1683147863566-ae457254f902?q=80&w=2070&auto=format&fit=crop&ixlib=rb-4.1.0&ixid=M3wxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8fA%3D%3D",
      "https://images.unsplash.com/photo-1490474418585-ba9bad8fd0ea?auto=format&fit=crop&w=1600&q=85"
    ],
  rating: "4.9",  
  stay: "1 night",
  guests: "2 guests",
  room: "Panoramic Deluxe",
  price: 115,

  includes: [
    "Daily breakfast",
    "Guided strawberry picking",
    "Fresh strawberry tasting"
  ],

  highlights: [
    "Welcome drink on arrival",
    "One-night Panoramic Deluxe accommodation",
    "Breakfast for two guests",
    "Guided visit to a strawberry farm",
    "Hand-picking strawberry experience",
    "Fresh strawberry tasting",
    "Complimentary Wi-Fi"
  ],

  schedule: [
    {
      time: "2:00 PM",
      title: "Arrival and check-in",
      text: "Check in to your Panoramic Deluxe room and enjoy a welcome drink."
    },
    {
      time: "3:30 PM",
      title: "Strawberry farm visit",
      text: "Travel to a nearby hillside strawberry farm with your guide."
    },
    {
      time: "4:00 PM",
      title: "Strawberry picking",
      text: "Walk through the farm and hand-pick fresh, ripe strawberries."
    },
    {
      time: "5:00 PM",
      title: "Fresh fruit tasting",
      text: "Enjoy freshly harvested strawberries and seasonal farm produce."
    }
  ],

  policies: [
    "Free cancellation up to 48 hours before arrival",
    "Farm activities may change due to weather or harvest conditions",
    "Check-in from 2:00 PM",
    "Package price applies to two guests"
  ]
},

  {
    id: 3,
    category: "Romantic Escape",
    title: "Romantic Hillside Stay",
    subtitle: "A quiet couple's retreat with beautiful hillside views.",
    description:
      "A quiet couple's stay with scenic views, breakfast and a private dinner.",

    longDescription:
      "Celebrate a special moment in a calm hillside setting. This two-night romantic package combines comfortable accommodation, thoughtful room decorations, daily breakfast and a private dinner created especially for couples.",

    image: `${baseUrl}images/img3.png`,

    gallery: [
      "https://images.unsplash.com/photo-1683435844264-365f588924c4?q=80&w=2070&auto=format&fit=crop&ixlib=rb-4.1.0&ixid=M3wxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8fA%3D%3D",
      "https://plus.unsplash.com/premium_photo-1674068280718-2f5b5c20a7ba?q=80&w=987&auto=format&fit=crop&ixlib=rb-4.1.0&ixid=M3wxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8fA%3D%3D",
      "https://images.unsplash.com/photo-1617364227571-a3ca7a580a0d?q=80&w=2072&auto=format&fit=crop&ixlib=rb-4.1.0&ixid=M3wxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8fA%3D%3D"
    ],

    rating: "4.6",
    stay: "2 nights",
    guests: "2 guests",
    room: "Garden Chalet",
    price: 245,

    includes: [
      "Daily breakfast",
      "Romantic room setup",
      "Private dinner"
    ],

    highlights: [
      "Two-night Garden Chalet accommodation",
      "Romantic room decoration",
      "Welcome drink for two",
      "Daily breakfast",
      "Private candlelight dinner",
      "Complimentary dessert",
      "Late check-out subject to availability"
    ],

    schedule: [
      {
        time: "2:00 PM",
        title: "Romantic arrival",
        text: "Check in to your decorated Garden Chalet."
      },
      {
        time: "5:00 PM",
        title: "Relax and unwind",
        text: "Enjoy the garden and scenic hillside surroundings."
      },
      {
        time: "7:30 PM",
        title: "Private dinner",
        text: "A private candlelight dinner prepared for two."
      },
      {
        time: "8:00 AM",
        title: "Breakfast",
        text: "Enjoy breakfast together in a peaceful resort setting."
      }
    ],

    policies: [
      "Free cancellation up to 72 hours before arrival",
      "Check-in from 2:00 PM",
      "Check-out before 11:00 AM",
      "Romantic setup must be requested before arrival"
    ]
  }
];
const benefits = [
  {
    icon: "fa-solid fa-circle-check",
    title: "Simple Packages",
    description: "Clear inclusions with no unnecessary complexity."
  },
  {
    icon: "fa-solid fa-utensils",
    title: "Local Dining",
    description: "Fresh meals inspired by Sri Lankan flavours."
  },
  {
    icon: "fa-solid fa-tree",
    title: "Nature Setting",
    description: "A calm stay surrounded by tropical greenery."
  },
  {
    icon: "fa-solid fa-headset",
    title: "Guest Support",
    description: "Friendly assistance before and during your stay."
  }
];

const galleryOpen = ref(false);
const activePackage = ref(null);

function openGallery(pkg) {
  activePackage.value = pkg;
  galleryOpen.value = true;
}

function closeGallery() {
  galleryOpen.value = false;

  window.setTimeout(() => {
    activePackage.value = null;
  }, 250);
}

function selectPackage(pkg) {
  emit("view-package", pkg);
}
</script>

<style scoped>

*{
  margin:0;
  padding:0;
  box-sizing:border-box;
  font-family: 'Figtree';
}

.packages-page {
  --primary: #134a8e;
  --primary-dark: #0c376e;
  --accent: #2476c8;
  --background: #f7f9fc;
  --soft-blue: #edf4fc;
  --white: #ffffff;
  --text: #1d2735;
  --muted: #6d7888;
  --border: #e2e7ed;
  --gold: #e4a72b;

  width: 100%;
  overflow: hidden;
  background: var(--white);
  color: var(--text);
  font-family: 'Figtree';
}

.packages-page *,
.packages-page *::before,
.packages-page *::after {
  box-sizing: border-box;
}

.page-container {
  width: min(92%, max(124px, 86.1111vw));
  margin: 0 auto;
  
}


.section-label,
.eyebrow {
  margin: 0 0 max(12px, 0.8333vw);
  color: var(--accent);
  font-size: max(1px, 0.6944vw);
  font-weight: 600;
  letter-spacing: max(1.5px, 0.1042vw);
  text-transform: uppercase;
}

.section-heading {
  max-width: max(65px, 45.1389vw);
  margin: 0 auto max(38px, 2.6389vw);
  text-align: center;
}

.eyebrow {
  color: #034acf;
  font-size: clamp(max(10px, 0.6944vw), 0.8vw, max(12px, 0.8333vw));
  font-weight: 700;
  letter-spacing: max(1px, 0.0694vw);
  margin-bottom: clamp(max(8px, 0.5556vw), 1vw, max(12px, 0.8333vw));
}

.section-heading h2,
.cta-box h2 {
  margin:0 auto;
   max-width:max(76px, 52.7778vw);
  margin: 0 0 max(1px, 0.6944vw);
  color: #1a51ad;
  font-size: max(3px, 2.0833vw);
  font-weight: 600;
}

.section-heading > p:last-child {
   margin-bottom: max(4px, 2.7778vw);
  color: #6b6b6b;
  font-size: max(9.5px, 0.6597vw);
  font-weight: 300;
  letter-spacing: max(0.4px, 0.0278vw);
}
.cta-box > div:first-child > p:last-child {
  margin: max(13px, 0.9028vw) 0 0;
  color: rgba(255, 255, 255, 0.76);
  font-size: max(14px, 0.9722vw);

  font-weight: 500;   /* Change this */
}


/* HERO */
.packages-hero {
  position: relative;
  width: 100%;
  min-height: max(3px, 20.8333vw);
  display: flex;
  align-items: center;
  overflow: hidden;
  padding: max(2px, 1.3889vw) max(2px, 1.3889vw) max(58px, 4.0278vw);
  background: #eaf6ff;



  
  
}


.hero-shell {
  width: min(92%, max(134px, 93.0556vw));
  margin: 0 auto;
  display: grid;
  grid-template-columns: minmax(max(34px, 23.6111vw), 0.78fr) minmax(max(52px, 36.1111vw), 1.22fr);
  align-items: center;
  gap: clamp(max(42px, 2.9167vw), 5vw, max(76px, 5.2778vw));
}

.hero-copy {
  max-width: max(53px, 36.8056vw);
}

.hero-copy .eyebrow {
  margin-bottom: max(15px, 1.0417vw);
}

.hero-copy h1 {
  margin: 0;
  color: var(--primary);
  font-size: clamp(max(42px, 2.9167vw), 4.5vw, max(62px, 4.3056vw));
  font-weight: 450;
  line-height: 1.04;
  letter-spacing: -1.7px;
}

.hero-copy h1 span {
  display: block;
}

.hero-description {
  max-width: max(52px, 36.1111vw);
  margin: max(23px, 1.5972vw) 0 max(29px, 2.0139vw);
  color: var(--muted);
  font-size: max(15px, 1.0417vw);
  line-height: 1.8;
}

.hero-button {
  min-height: max(48px, 3.3333vw);
  display: inline-flex;
  align-items: center;
  justify-content: center;
  gap: max(9px, 0.625vw);
  padding: max(12px, 0.8333vw) max(22px, 1.5278vw);
  border-radius: max(7px, 0.4861vw);
  background: var(--primary);
  color: #ffffff;
  font-size: max(13px, 0.9028vw);
  font-weight: 700;
  text-decoration: none;
  box-shadow: 0 max(1px, 0.6944vw) max(24px, 1.6667vw) rgba(19, 74, 142, 0.16);
  transition:
    background 0.25s ease,
    transform 0.25s ease,
    box-shadow 0.25s ease;
}

.hero-button:hover {
  background: var(--primary-dark);
  transform: translateY(-2px);
  box-shadow: 0 max(14px, 0.9722vw) max(28px, 1.9444vw) rgba(19, 74, 142, 0.22);
}

.hero-visual {
  position: relative;
  height: clamp(max(41px, 28.4722vw), 38vw, max(525px, 36.4583vw));
  overflow: hidden;
  border-radius: max(18px, 1.25vw);
  background: #dbe5ef;
  box-shadow: 0 max(2px, 1.3889vw) max(48px, 3.3333vw) rgba(31, 58, 88, 0.12);
}

.hero-visual > img {
  width: 100%;
  height: 100%;
  display: block;
  object-fit: cover;
  transition: transform 0.6s ease;
}

.hero-visual:hover > img {
  transform: scale(1.025);
}

.hero-location {
  position: absolute;
  left: max(24px, 1.6667vw);
  bottom: max(22px, 1.5278vw);
  display: flex;
  align-items: center;
  gap: max(11px, 0.7639vw);
  padding: max(13px, 0.9028vw) max(16px, 1.1111vw);
  border: max(1px, 0.0694vw) solid rgba(255, 255, 255, 0.72);
  border-radius: max(11px, 0.7639vw);
  background: rgba(255, 255, 255, 0.93);
  color: var(--primary);
  box-shadow: 0 max(1px, 0.6944vw) max(25px, 1.7361vw) rgba(17, 39, 67, 0.13);
  backdrop-filter: blur(max(12px, 0.8333vw));
}

.hero-location-icon {
  width: max(34px, 2.3611vw);
  height: max(34px, 2.3611vw);
  display: grid;
  place-items: center;
  flex: 0 0 auto;
  border-radius: 50%;
  background: var(--soft-blue);
  font-size: max(17px, 1.1806vw);
}

.hero-location small,
.hero-location strong {
  display: block;
}

.hero-location small {
  margin-bottom: max(3px, 0.2083vw);
  color: var(--muted);
  font-size: max(9px, 0.625vw);
}

.hero-location strong {
  font-size: max(12px, 0.8333vw);
}

/* PACKAGES */

.packages-section {
    padding: max(25px, 1.7361vw) 0 max(78px, 5.4167vw);
    background: var(--background);
}

.packages-grid {
  display: grid;
  grid-template-columns: repeat(3, minmax(0, 1fr));
  gap: max(24px, 1.6667vw);
}

.package-card {
  display: flex;
  min-width: 0;
  overflow: hidden;
  flex-direction: column;
  border: max(1px, 0.0694vw) solid var(--border);
  border-radius: max(16px, 1.1111vw);
  background: #ffffff;
  transition: 0.28s ease;
}

.package-card:hover {
  transform: translateY(-5px);
  box-shadow: 0 max(18px, 1.25vw) max(42px, 2.9167vw) rgba(35, 58, 85, 0.11);
}

.package-image {
  position: relative;
  width: 100%;
  height: max(25px, 17.3611vw);
  display: block;
  overflow: hidden;
  padding: 0;
  border: 0;
  background: #dce5ef;
  color: inherit;
  cursor: pointer;
  text-align: left;
}

.package-image img {
  width: 100%;
  height: 100%;
  display: block;
  object-fit: cover;
  transition: transform 0.5s ease;
}

.package-card:hover .package-image img,
.package-image:hover img,
.package-image:focus-visible img {
  transform: scale(1.06);
}

.package-image-shade {
  position: absolute;
  inset: 0;
  z-index: 1;
  background: linear-gradient(
    180deg,
    rgba(4, 20, 42, 0.02) 35%,
    rgba(4, 20, 42, 0.62) 100%
  );
  opacity: 0;
  transition: opacity 0.3s ease;
}

/* FINAL CLEAN VIEW GALLERY HOVER */

.gallery-hover {
  position: absolute !important;
  inset: 0 !important;

  left: 0 !important;
  right: 0 !important;
  top: 0 !important;
  bottom: 0 !important;

  width: 100%;
  height: 100%;

  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  gap: max(1px, 0.6944vw);

  min-width: 0;
  padding: 0;

  border: 0;
  border-radius: 0;
  background: rgba(5, 20, 40, 0.35);

  color: #ffffff;
  box-shadow: none;
  backdrop-filter: none;

  opacity: 0;
  transform: none !important;

  z-index: 4;
  transition: opacity 0.3s ease;
}

.gallery-hover-icon {
  width: max(46px, 3.1944vw);
  height: max(46px, 3.1944vw);

  display: flex;
  align-items: center;
  justify-content: center;

  flex: 0 0 auto;

  border-radius: 50%;
  background: rgba(255, 255, 255, 0.96);
  color: #134a8e;

  font-size: max(2px, 1.3889vw);
  box-shadow: 0 max(5px, 0.3472vw) max(18px, 1.25vw) rgba(0, 0, 0, 0.16);

  transform: translateY(max(5px, 0.3472vw));
  transition: transform 0.3s ease;
}

.gallery-hover strong {
  display: block;
  margin: 0;

  color: #ffffff !important;
  font-size: max(14px, 0.9722vw);
  font-weight: 600;
  line-height: 1.2;
  letter-spacing: max(0.3px, 0.0208vw);
  text-align: center;
  white-space: nowrap;

  text-shadow: 0 max(2px, 0.1389vw) max(8px, 0.5556vw) rgba(0, 0, 0, 0.45);
}

.gallery-hover small {
  display: none !important;
}

.package-image:hover .gallery-hover,
.package-image:focus-visible .gallery-hover {
  opacity: 1;
  transform: none !important;
}

.package-image:hover .gallery-hover-icon,
.package-image:focus-visible .gallery-hover-icon {
  transform: translateY(0);
}

.package-image-shade{
    display:none;
}

.package-image:hover .package-image-shade,
.package-image:focus-visible .package-image-shade,
.package-image:hover .gallery-hover,
.package-image:focus-visible .gallery-hover {
  opacity: 1;
}


.package-image:focus-visible {
  outline: max(3px, 0.2083vw) solid rgba(36, 118, 200, 0.34);
  outline-offset: -3px;
}

.package-category {
  position: absolute;
  top: max(15px, 1.0417vw);
  left: max(15px, 1.0417vw);
  padding: max(7px, 0.4861vw) max(11px, 0.7639vw);
  border-radius: max(2px, 1.3889vw);
  background: rgba(255, 255, 255, 0.94);
  color: var(--primary);
  font-size: max(1px, 0.6944vw);
  font-weight: 700;
}

.package-content {
  display: flex;
  flex: 1;
  flex-direction: column;
  padding: max(23px, 1.5972vw);
}

.package-heading {
  display: flex;
  align-items: flex-start;
  justify-content: space-between;
  gap: max(13px, 0.9028vw);
}

.package-heading > div {
  min-width: 0;
}

.package-heading h3 {
  margin: 0 0 max(6px, 0.4167vw);
  font-size: max(2px, 1.3889vw);
  font-weight: 550;
  color:  #073C94;
}

.package-heading p {
  margin: max(9px, 0.625vw) 0 0;
  color:  #5d5d5d;
  font-size: max(12.5px, 0.8681vw);
  line-height: 1.65;

}

.package-rating {
  flex: 0 0 auto;
  display: inline-flex;
  align-items: center;
  gap: max(5px, 0.3472vw);
  color: var(--text);
  font-size: max(11px, 0.7639vw);
  font-weight: 700;
}

.package-rating i {
  color: var(--gold);
  fill: currentColor;
}

.package-meta {
  display: flex;
  flex-wrap: wrap;
  gap: max(1px, 0.6944vw) max(15px, 1.0417vw);
  margin-top: max(19px, 1.3194vw);
  padding: max(14px, 0.9722vw) 0;
  border-top: max(1px, 0.0694vw) solid var(--border);
  border-bottom: max(1px, 0.0694vw) solid var(--border);
}

.package-meta span {
  display: inline-flex;
  align-items: center;
  gap: max(6px, 0.4167vw);
  color: #050505;
  font-size: max(11px, 0.7639vw);
  font-weight: 500;
}

.package-meta i {
  color: var(--accent);
  font-size: max(15px, 1.0417vw);
}

.package-includes {
  display: grid;
  gap: max(9px, 0.625vw);
  margin: max(18px, 1.25vw) 0 0;
  padding: 0;
  list-style: none;
}

.package-includes li {
  display: flex;
  align-items: center;
  gap: max(7px, 0.4861vw);
  color:  #5d5d5d;
  font-size: max(12px, 0.8333vw);
   margin: max(9px, 0.625vw) 0 0;
  
}

.package-includes i {
  color: var(--accent);
  font-size: max(14px, 0.9722vw);
}

.package-footer {
  display: flex;
  align-items: flex-end;
  justify-content: space-between;
  gap: max(15px, 1.0417vw);
  margin-top: auto;
  padding-top: max(23px, 1.5972vw);
}

.package-price small,
.package-price span {
  display: flex;
  align-items: center;
  gap: max(7px, 0.4861vw);
  color:  #5d5d5d;
  font-size: max(12px, 0.8333vw);
   margin: max(9px, 0.625vw) 0 0;
}

.package-price strong {
  display: block;
  margin: max(3px, 0.2083vw) 0;
  color: #073C94;
 line-height: 1;
  font-size: max(3px, 2.0833vw);
  font-weight: 600;

}

.package-button {
  min-height: max(44px, 3.0556vw);
  display: inline-flex;
  align-items: center;
  justify-content: center;
  gap: max(7px, 0.4861vw);
  padding: max(11px, 0.7639vw) max(15px, 1.0417vw);
  border: 0;
  border-radius: max(7px, 0.4861vw);
  background: #021c44;
  color: #ffffff;
  cursor: pointer;
  font-size: max(12px, 0.8333vw);
  font-weight: 400;
  transition: 0.25s ease;
  flex-wrap: wrap;

  
}

.package-button:hover {
  background: #032d6b;
}




--------------------------


.pill {
  height: max(38px, 2.6389vw);
  padding: 0 max(18px, 1.25vw);
  border-radius: max(1px, 0.6944vw);
  border: max(1px, 0.0694vw) solid var(--color-line);
  background: #fff;
  color: var(--color-ink);
 
  font-size: max(12px, 0.8333vw);
  font-weight: 500;
  cursor: pointer;
  transition: background 0.15s ease, border-color 0.15s ease, color 0.15s ease;
}

.pill:hover {
  border-color: var(--color-pine);
}

.pill.active {
  background: var(--color-pine);
  border-color: var(--color-pine);
  color: #fff;
}

/* BENEFITS */

.benefits-section {
  padding: max(72px, 5vw) 0;
}

.benefits-grid {
  display: grid;
  grid-template-columns: repeat(4, 1fr);
  gap: max(18px, 1.25vw);
}

.benefit-item {
  display: flex;
  align-items: flex-start;
  gap: max(13px, 0.9028vw);
  padding: max(22px, 1.5278vw);
  border: max(1px, 0.0694vw) solid var(--border);
  border-radius: max(12px, 0.8333vw);
}

.benefit-icon {
  width: max(39px, 2.7083vw);
  height: max(39px, 2.7083vw);
  display: grid;
  place-items: center;
  flex: 0 0 auto;
  border-radius: 50%;
  background: var(--soft-blue);
  color: var(--primary);
  font-size: max(18px, 1.25vw);
}

.benefit-item h3 {
  margin: max(2px, 0.1389vw) 0 max(7px, 0.4861vw);
  color: var(--primary);
  font-size: max(14px, 0.9722vw);
  font-weight: 600;
}

.benefit-item p {
  margin: 0;
  color: var(--muted);
  font-size: max(12px, 0.8333vw);
  line-height: 1.6;
}

/* CTA */

.cta-section {
  padding: 0 0 max(8px, 5.5556vw);
}

.cta-box .cta-description {
  margin: max(13px, 0.9028vw) 0 0 !important;
  color: rgba(255, 255, 255, 0.76) !important;
  font-size: max(14px, 0.9722vw) !important;
  font-weight: 100 !important;
  line-height: 1.7 !important;
}

.cta-box {
  display: flex;
  align-items: center;
  justify-content: space-between;
  gap: max(4px, 2.7778vw);
  padding: max(42px, 2.9167vw) max(46px, 3.1944vw);
  border-radius: max(14px, 0.9722vw);
  background: var(--primary);
  color: #ffffff;
}

.cta-box .section-label {
  color: #c7dcf5;
}

.cta-box h2 {
  color: #ffffff;
  font-size: max(25px, 1.7361vw);
  font-weight: 300;
}


.cta-actions {
  display: flex;
  flex: 0 0 auto;
  gap: max(1px, 0.6944vw);
}

.cta-primary,
.cta-secondary {
  min-height: max(44px, 3.0556vw);
  display: inline-flex;
  align-items: center;
  justify-content: center;
  gap: max(7px, 0.4861vw);
  padding: max(11px, 0.7639vw) max(17px, 1.1806vw);
  border-radius: max(7px, 0.4861vw);
  font-size: max(12px, 0.8333vw);
  font-weight: 600;
  text-decoration: none;
}

.cta-primary {
  background: #ffffff;
  color: var(--primary);
}

.cta-secondary {
  border: max(1px, 0.0694vw) solid rgba(255, 255, 255, 0.5);
  color: #ffffff;
}

/* HERO RESPONSIVE */

@media (max-width: 980px) {
  .hero-shell {
    grid-template-columns: 1fr;
    gap: max(36px, 2.5vw);
  }

  .hero-copy {
    max-width: max(68px, 47.2222vw);
  }

  .hero-visual {
    height: max(44px, 30.5556vw);
  }
}

@media (max-width: 680px) {
  .packages-hero {
    padding: max(48px, 3.3333vw) 0 max(56px, 3.8889vw);
  }

  .hero-shell {
    width: 92%;
    gap: max(3px, 2.0833vw);
  }

  .hero-copy h1 {
    font-size: clamp(max(36px, 2.5vw), 11vw, max(48px, 3.3333vw));
    letter-spacing: -1px;
  }

  .hero-description {
    margin: max(19px, 1.3194vw) 0 max(25px, 1.7361vw);
    font-size: max(14px, 0.9722vw);
  }

  .hero-visual {
    height: max(33px, 22.9167vw);
    border-radius: max(14px, 0.9722vw);
  }

  .hero-location {
    right: max(14px, 0.9722vw);
    left: max(14px, 0.9722vw);
    bottom: max(14px, 0.9722vw);
  }
}
/* ======================================
   PREMIUM PACKAGE HERO
====================================== */


/* ==========================================
   PREMIUM PACKAGE HERO
========================================== */

.packages-hero{
    position:relative;
    overflow:hidden;
    background:linear-gradient(180deg,#eef8ff 0%,#dfeffc 100%);
    padding:max(7px, 4.8611vw) max(2px, 1.3889vw) max(85px, 5.9028vw);
}

/* Wave 1 */

.hero-bg-1{
    position:absolute;
    top:-70px;
    left:-10%;
    width:120%;
    height:max(25px, 17.3611vw);
    background:rgba(255,255,255,.38);
    border-radius:0 0 50% 50%;
    transform:rotate(-2deg);
    
}

/* Wave 2 */

.hero-bg-2{
    position:absolute;
    bottom:-120px;
    left:-12%;
    width:124%;
    height:max(26px, 18.0556vw);
    background:rgba(255,255,255,.55);
    border-radius:50% 50% 0 0;
    transform:rotate(2deg);
}

/* Extra soft wave */

.packages-hero::before{
    content:"";
    position:absolute;
    top:max(9px, 6.25vw);
    left:-8%;
    width:116%;
    height:max(18px, 12.5vw);
    background:rgba(255,255,255,.18);
    border-radius:50%;
    transform:rotate(4deg);
}

/* Extra bottom wave */

.packages-hero::after{
    content:"";
    position:absolute;
    bottom:-70px;
    right:-10%;
    width:120%;
    height:max(18px, 12.5vw);
    background:rgba(255,255,255,.25);
    border-radius:50%;
    transform:rotate(-3deg);
}

.hero-content{
    position:relative;
    z-index:5;
    max-width:max(76px, 52.7778vw);
    margin:auto;
    text-align:center;
    
    
}

.hero-badge{
    display:inline-flex;
    align-items:center;
    justify-content:center;
    
    padding:max(1px, 0.6944vw) max(26px, 1.8056vw);

    border-radius:max(4px, 2.7778vw);

    background:rgba(255,255,255,.75);

    backdrop-filter:blur(max(1px, 0.6944vw));

    border:max(1px, 0.0694vw) solid rgba(19,74,142,.08);

    color:#134a8e;

    font-size:max(11px, 0.7639vw);

    font-weight:700;

    letter-spacing:max(2px, 0.1389vw);

    margin-bottom:max(26px, 1.8056vw);
}

.hero-content h1{

    margin:0 auto;
   max-width:max(76px, 52.7778vw);
  margin: 0 0 max(1px, 0.6944vw);
  color: #1a51ad;
  font-size: max(39px, 2.7083vw);
  font-weight: 600;
}

.hero-content p{
  margin-bottom: max(4px, 2.7778vw);
  color: #6b6b6b;
  font-size: max(12.5px, 0.8681vw);
  font-weight: 300;
  letter-spacing: max(0.4px, 0.0278vw);
}

/* Tablet */

@media(max-width:900px){

.packages-hero{
    padding:max(6px, 4.1667vw) max(2px, 1.3889vw) max(7px, 4.8611vw);
}

.hero-bg-1{
    height:max(18px, 12.5vw);
}

.hero-bg-2{
    height:max(2px, 13.8889vw);
}

.hero-content h1{
    font-size:max(48px, 3.3333vw);
}

.hero-content p{
    font-size:max(16px, 1.1111vw);
}

}

/* Mobile */

@media(max-width:600px){

.packages-hero{
    padding:max(5px, 3.4722vw) max(18px, 1.25vw) max(6px, 4.1667vw);
}

.hero-badge{
    padding:max(8px, 0.5556vw) max(2px, 1.3889vw);
    font-size:max(1px, 0.6944vw);
}

.hero-content h1{
    font-size:max(36px, 2.5vw);
}

.hero-content p{
    font-size:max(15px, 1.0417vw);
}

.hero-bg-1,
.hero-bg-2{
    height:max(15px, 10.4167vw);
}

}

/* Tablet */

@media(max-width:900px){

.packages-hero{

    padding:max(9px, 6.25vw) max(2px, 1.3889vw) max(11px, 7.6389vw);

}

.hero-content h1{

    font-size:max(48px, 3.3333vw);

}

.hero-content p{

    font-size:max(18px, 1.25vw);

}

}

/* Mobile */

@media(max-width:600px){

.packages-hero{

    padding:max(7px, 4.8611vw) max(18px, 1.25vw) max(9px, 6.25vw);

}

.hero-badge{

    font-size:max(11px, 0.7639vw);

    padding:max(1px, 0.6944vw) max(22px, 1.5278vw);

}

.hero-content h1{

    font-size:max(38px, 2.6389vw);

}

.hero-content p{

    font-size:max(16px, 1.1111vw);

}

}


/* PACKAGE GALLERY MODAL */

:global(body.gallery-lock) {
  overflow: hidden;
}

.gallery-modal {
  position: fixed;
  inset: 0;
  z-index: 9999;
  display: grid;
  place-items: center;
  padding: clamp(max(18px, 1.25vw), 3vw, max(42px, 2.9167vw));
  background: rgba(8, 17, 29, 0.72);
  backdrop-filter: blur(max(8px, 0.5556vw));
}

.gallery-dialog {
  width: min(94vw, max(94px, 65.2778vw));
  max-height: 92vh;
  overflow: hidden;
  border: max(1px, 0.0694vw) solid rgba(255, 255, 255, 0.58);
  border-radius: max(2px, 1.3889vw);
  background: #ffffff;
  box-shadow: 0 max(34px, 2.3611vw) max(9px, 6.25vw) rgba(0, 0, 0, 0.32);
}

.gallery-header {
  min-height: max(9px, 6.25vw);
  display: flex;
  align-items: center;
  justify-content: space-between;
  gap: max(2px, 1.3889vw);
  padding: max(2px, 1.3889vw) max(26px, 1.8056vw);
  border-bottom: max(1px, 0.0694vw) solid var(--border);
}

.gallery-eyebrow {
  display: block;
  margin-bottom: max(4px, 0.2778vw);
  color: #034acf;
  font-size: max(9px, 0.625vw);
  font-weight: 700;
  letter-spacing: max(1.4px, 0.0972vw);
  text-transform: uppercase;
}

.gallery-header h2 {
  margin: 0;
  color: #1A51AD;
  font-size: clamp(max(21px, 1.4583vw), 2vw, max(28px, 1.9444vw));
  font-weight: 500;
}

.gallery-close {
  width: max(42px, 2.9167vw);
  height: max(42px, 2.9167vw);
  display: grid;
  place-items: center;
  flex: 0 0 auto;
  padding: 0;
  border: 0;
  border-radius: 50%;
  background: #f2f5f8;
  color: #5f6b79;
  cursor: pointer;
  font-size: max(2px, 1.3889vw);
  transition:
    background 0.2s ease,
    color 0.2s ease,
    transform 0.2s ease;
}

.gallery-close:hover {
  background: var(--soft-blue);
  color: var(--primary);
  transform: rotate(4deg);
}

.gallery-body {
  padding: clamp(max(18px, 1.25vw), 3vw, max(3px, 2.0833vw));
}

.gallery-stage {
  position: relative;

  width: 100%;
  max-height: 70vh;

  display: flex;
  justify-content: center;
  align-items: center;

  overflow: hidden;
}

.gallery-stage img{
    width:auto;
    height:auto;

    max-width:100%;
    max-height:70vh;

    object-fit:contain;
}

.gallery-arrow {
  position: absolute;
  top: 50%;
  z-index: 2;
  width: max(46px, 3.1944vw);
  height: max(46px, 3.1944vw);
  display: grid;
  place-items: center;
  padding: 0;
  border: max(1px, 0.0694vw) solid rgba(255, 255, 255, 0.55);
  border-radius: 50%;
  background: rgba(255, 255, 255, 0.92);
  color: #172231;
  cursor: pointer;
  font-size: max(22px, 1.5278vw);
  box-shadow: 0 max(8px, 0.5556vw) max(22px, 1.5278vw) rgba(0, 0, 0, 0.16);
  transform: translateY(-50%);
  backdrop-filter: blur(max(1px, 0.6944vw));
  transition:
    background 0.2s ease,
    transform 0.2s ease;
}

.gallery-arrow:hover {
  background: #ffffff;
  transform: translateY(-50%) scale(1.06);
}

.gallery-arrow-left {
  left: max(18px, 1.25vw);
}

.gallery-arrow-right {
  right: max(18px, 1.25vw);
}

.gallery-counter {
  position: absolute;
  left: 50%;
  bottom: max(16px, 1.1111vw);
  z-index: 2;
  padding: max(7px, 0.4861vw) max(13px, 0.9028vw);
  border-radius: max(999px, 69.375vw);
  background: rgba(10, 18, 29, 0.74);
  color: #ffffff;
  font-size: max(11px, 0.7639vw);
  font-weight: 600;
  transform: translateX(-50%);
  backdrop-filter: blur(max(8px, 0.5556vw));
}

.gallery-thumbnails {
  display: flex;
  justify-content: center;
  gap: max(9px, 0.625vw);
  margin-top: max(14px, 0.9722vw);
  overflow-x: auto;
  padding: max(2px, 0.1389vw) max(2px, 0.1389vw) max(4px, 0.2778vw);
}

.gallery-thumbnail {
  width: max(76px, 5.2778vw);
  height: max(54px, 3.75vw);
  flex: 0 0 auto;
  overflow: hidden;
  padding: 0;
  border: max(2px, 0.1389vw) solid transparent;
  border-radius: max(8px, 0.5556vw);
  background: #e7edf3;
  cursor: pointer;
  opacity: 0.62;
  transition:
    border-color 0.2s ease,
    opacity 0.2s ease,
    transform 0.2s ease;
}

.gallery-thumbnail:hover,
.gallery-thumbnail.active {
  border-color: var(--accent);
  opacity: 1;
  transform: translateY(-2px);
}

.gallery-thumbnail img {
  width: 100%;
  height: 100%;
  display: block;
  object-fit: cover;
}

.gallery-modal-enter-active,
.gallery-modal-leave-active {
  transition: opacity 0.22s ease;
}

.gallery-modal-enter-active .gallery-dialog,
.gallery-modal-leave-active .gallery-dialog {
  transition:
    opacity 0.22s ease,
    transform 0.22s ease;
}

.gallery-modal-enter-from,
.gallery-modal-leave-to {
  opacity: 0;
}

.gallery-modal-enter-from .gallery-dialog,
.gallery-modal-leave-to .gallery-dialog {
  opacity: 0;
  transform: translateY(max(18px, 1.25vw)) scale(0.98);
}

@media (max-width: 680px) {
  .gallery-modal {
    padding: max(12px, 0.8333vw);
  }

  .gallery-dialog {
    width: 100%;
    max-height: 95vh;
    border-radius: max(16px, 1.1111vw);
  }

  .gallery-header {
    min-height: max(76px, 5.2778vw);
    padding: max(15px, 1.0417vw) max(17px, 1.1806vw);
  }

  .gallery-body {
    padding: max(12px, 0.8333vw);
  }

  .gallery-stage {
    height: min(58vh, max(48px, 33.3333vw));
    border-radius: max(11px, 0.7639vw);
  }

  .gallery-arrow {
    width: max(4px, 2.7778vw);
    height: max(4px, 2.7778vw);
  }

  .gallery-arrow-left {
    left: max(1px, 0.6944vw);
  }

  .gallery-arrow-right {
    right: max(1px, 0.6944vw);
  }

  .gallery-hover {
    opacity: 1;
    transform: translate(-50%, 0);
  }

  .package-image-shade {
    opacity: 1;
  }
}


/* RESPONSIVE */

@media (max-width: 1050px) {
  .packages-grid {
    grid-template-columns: repeat(2, minmax(0, 1fr));
  }

  .benefits-grid {
    grid-template-columns: repeat(2, 1fr);
  }
}

@media (max-width: 760px) {
  .packages-hero {
    padding: max(78px, 5.4167vw) 0;
  }

  .hero-content h1 {
    font-size: clamp(max(35px, 2.4306vw), 10vw, max(47px, 3.2639vw));
  }

  .packages-section,
  .benefits-section {
    padding: max(58px, 4.0278vw) 0;
  }

  .packages-grid {
    grid-template-columns: 1fr;
  }

  .package-image {
    height: max(24px, 16.6667vw);
  }

  .cta-box {
    align-items: flex-start;
    flex-direction: column;
  }

  .cta-actions {
    width: 100%;
  }

  .cta-primary,
  .cta-secondary {
    flex: 1;
  }
}

@media (max-width: 520px) {
  .package-footer {
    align-items: stretch;
    flex-direction: column;
  }

  .package-button {
    width: 100%;
  }

  .benefits-grid {
    grid-template-columns: 1fr;
  }

  .cta-box {
    padding: max(31px, 2.1528vw) max(23px, 1.5972vw);
  }

  .cta-actions {
    flex-direction: column;
  }

  .cta-primary,
  .cta-secondary {
    width: 100%;
  }
}
</style>
