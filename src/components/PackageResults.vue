<template>
  <section class="packages-section">
    <div class="section-header">
      <div class="eyebrow">— PACKAGES</div>
      <h2>Choose your package</h2>

      <div class="bar-wrap">
        <AvailabilityBar
          v-model:start-date="startDate"
          v-model:end-date="endDate"
          v-model:travelers="travelers"
          v-model:room-count="roomCount"
          v-model:filter="filter"
          :total="packages.length"
          :shown="filteredPackages.length"
        />
      </div>

      <p class="subhead">
        {{ packages.length }} curated packages · accommodation and experiences included
      </p>
    </div>

    <article
      v-for="pkg in filteredPackages"
      :key="pkg.id"
      class="package-card"
    >
      <div
        class="package-image-box"
        role="button"
        tabindex="0"
        :aria-label="`View ${pkg.title} details`"
        @click="openPackageDetails(pkg)"
        @keydown.enter="openPackageDetails(pkg)"
        @keydown.space.prevent="openPackageDetails(pkg)"
      >
        <img
          :src="pkg.image"
          :alt="pkg.title"
          class="package-image"
        />
        <span class="package-tag">{{ pkg.category }}</span>
      </div>

      <div class="package-details">
        <div class="package-title">
          <div class="package-title-main">
            <h3>{{ pkg.title }}</h3>

            <span
              v-if="pkg.featured"
              class="badge"
            >
              RECOMMENDED
            </span>
          </div>

          <span class="rating-badge">
            <i class="fa-solid fa-star"></i>
            {{ pkg.rating }}
          </span>
        </div>

        <p class="package-description">
          {{ pkg.description }}
        </p>

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

        <div class="options-row">
          <div class="option">
            <label>Package includes</label>

            <div class="includes-card">
              <div class="includes-list">
                <span
                  v-for="item in pkg.includes"
                  :key="item"
                  class="include-item"
                >
                  <i :class="getIncludeIcon(item)"></i>
                  {{ item }}
                </span>
              </div>

              <button
                type="button"
                class="see-more-btn"
                @click="openPackageDetails(pkg)"
              >
                See More
              </button>
            </div>
          </div>
        </div>
      </div>

      <div class="divider" aria-hidden="true"></div>

      <div class="package-options">
        <span class="price-label">Package from</span>

        <div class="price-row">
          <span class="price">${{ pkg.price }}</span>
          <span class="price-caption">total</span>
        </div>

        <p class="price-breakdown">
          Taxes and package inclusions covered
        </p>

        <button
          type="button"
          class="select-package-btn"
          @click="selectPackage(pkg)"
        >
          Purchase this package
        </button>

      </div>
    </article>


  </section>
</template>

<script setup>
import { ref, computed } from "vue";
import AvailabilityBar from "./AvailabilityBar.vue";

const emit = defineEmits(["checkout-package", "show-packages"]);
const baseUrl = import.meta.env.BASE_URL;

const startDate = ref("");
const endDate = ref("");
const travelers = ref(2);
const roomCount = ref(1);
const filter = ref("all");

const packages = [
  {
    id: 1,
    category: "Nature Escape",
    title: "Signature Nature Stay",
    description:
      "A peaceful stay with breakfast, a guided nature walk and private dining.",
    longDescription:
      "Reconnect with nature through a carefully planned resort experience with comfortable accommodation, breakfast, a guided walk and private dining.",
    image: `${baseUrl}images/img1.png`,
    rating: "4.9",
    stay: "1 night",
    guests: "2 guests",
    room: "Luxury Deluxe",
    price: 150,
    featured: true,
    includes: [
      "Breakfast for two",
      "Guided nature walk",
      "Private dinner"
    ],
    highlights: [
      "Welcome drink",
      "Luxury Deluxe accommodation",
      "Breakfast for two",
      "Guided nature walk",
      "Private dinner",
      "Complimentary Wi-Fi"
    ]
  },
  {
    id: 2,
    category: "Farm Experience",
    title: "Strawberry Harvest & Stay",
    description:
      "A relaxing hillside stay with a guided strawberry-picking and farm-fresh tasting experience.",
    longDescription:
      "Enjoy a peaceful resort stay combined with a visit to a nearby strawberry farm. Learn how strawberries are grown, hand-pick ripe fruit and enjoy a fresh tasting surrounded by beautiful hillside scenery.",
    image: `${baseUrl}images/img2.png`,
    rating: "4.8",
    stay: "1 night",
    guests: "2 guests",
    room: "Panoramic Deluxe",
    price: 115,
    featured: false,
    includes: [
      "Daily breakfast",
      "Guided strawberry picking",
      "Strawberry tasting"
    ],
    highlights: [
      "Welcome drink",
      "Panoramic Deluxe accommodation",
      "Breakfast for two",
      "Guided strawberry farm visit",
      "Hand-picking experience",
      "Fresh strawberry tasting"
    ]
  },
  {
    id: 3,
    category: "Romantic Escape",
    title: "Romantic Hillside Stay",
    description:
      "A quiet couple's stay with scenic views, breakfast and a private dinner.",
    longDescription:
      "Celebrate a special moment in a calm hillside setting with two nights, room decoration, breakfast and a private dinner.",
    image: `${baseUrl}images/img3.png`,
    rating: "4.9",
    stay: "2 nights",
    guests: "2 guests",
    room: "Garden Chalet",
    price: 245,
    featured: false,
    includes: [
      "Daily breakfast",
      "Romantic room setup",
      "Private dinner"
    ],
    highlights: [
      "Garden Chalet accommodation",
      "Romantic decoration",
      "Welcome drink",
      "Daily breakfast",
      "Candlelight dinner",
      "Late checkout subject to availability"
    ]
  }
];

const filteredPackages = computed(() => {
  if (filter.value === "all") {
    return packages;
  }

  const selectedFilter = String(filter.value).toLowerCase();

  return packages.filter((pkg) => {
    return (
      pkg.category.toLowerCase().includes(selectedFilter) ||
      pkg.title.toLowerCase().includes(selectedFilter) ||
      pkg.room.toLowerCase().includes(selectedFilter)
    );
  });
});

function openPackageDetails(pkg) {
  emit("show-packages", pkg);
}

function getIncludeIcon(item) {
  const text = item.toLowerCase();

  if (text.includes("breakfast") || text.includes("dinner") || text.includes("tasting")) {
    return "fa-solid fa-utensils";
  }

  if (text.includes("walk") || text.includes("farm") || text.includes("picking")) {
    return "fa-solid fa-person-hiking";
  }

  if (text.includes("room") || text.includes("accommodation") || text.includes("setup")) {
    return "fa-solid fa-bed";
  }

  if (text.includes("wifi") || text.includes("wi-fi")) {
    return "fa-solid fa-wifi";
  }

  if (text.includes("welcome drink")) {
    return "fa-solid fa-martini-glass-citrus";
  }

  return "fa-solid fa-circle-check";
}

function createBookingPayload(pkg) {
  return {
    ...pkg,
    startDate: startDate.value,
    endDate: endDate.value,
    travelers: travelers.value,
    roomCount: roomCount.value
  };
}

function selectPackage(pkg) {
  emit("checkout-package", createBookingPayload(pkg));
}

</script>

<style scoped>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
  font-family: "Figtree", sans-serif;
}

.eyebrow {
  color: #034acf;
  font-size: clamp(max(10px, 0.6944vw), 0.8vw, max(12px, 0.8333vw));
  font-weight: 700;
  letter-spacing: max(1px, 0.0694vw);
  margin-bottom: clamp(max(8px, 0.5556vw), 1vw, max(12px, 0.8333vw));
}

.packages-section {
  --color-surface: #ffffff;
  --color-ink: #050505;
  --color-ink-soft: #5d5d5d;
  --color-line: #787c79;
  --color-pine: #073c94;

  max-width: max(1340px, 93.0556vw);
  padding: max(50px, 3.4722vw);
  background: #f8f9fb;
  color: var(--color-ink);
}

.packages-section h2 {
  margin-bottom: max(55px, 3.8194vw);
  color: #1a51ad;
  font-size: max(34px, 2.3611vw);
  font-weight: 500;
  line-height: 1.3;
}

.bar-wrap {
  width: 100%;
}

.availability-field {
  display: flex;
  align-items: center;
  gap: max(11px, 0.7639vw);
  padding: 0 max(16px, 1.1111vw);
  border: max(1px, 0.0694vw) solid #cfd7e2;
  background: #ffffff;
  color: #303948;
  font-size: max(13px, 0.9028vw);
  text-align: left;
  cursor: pointer;
}

.availability-field svg {
  color: #1e7bdd;
}

.availability-button {
  border: 0;
  background: #2d7bd6;
  color: #ffffff;
  font-size: max(13px, 0.9028vw);
  font-weight: 700;
  cursor: pointer;
}

.subhead {
  margin-bottom: max(20px, 1.3889vw);
  color: var(--color-ink-soft);
  font-size: max(12px, 0.8333vw);
}

.package-card {
  position: relative;
  display: grid;
  grid-template-columns: max(300px, 20.8333vw) minmax(0, 1fr) max(24px, 1.6667vw) max(260px, 18.0556vw);
  width: 100%;
  min-height: max(342px, 23.75vw);
  margin-bottom: max(28px, 1.9444vw);
  overflow: hidden;
  border-radius: max(20px, 1.3889vw);
  background: #ffffff;
  box-shadow: 0 max(1px, 0.0694vw) max(2px, 0.1389vw) rgba(31, 46, 36, 0.04);
}


.package-card:hover {
  box-shadow: 0 max(18px, 1.25vw) max(40px, 2.7778vw) rgba(31, 46, 36, 0.1);
  transform: translateY(-2px);
}

.package-image-box {
  position: relative;
  cursor: pointer;
  width: max(300px, 20.8333vw);
  height: max(342px, 23.75vw);
  min-height: max(342px, 23.75vw);
  overflow: hidden;
  border-radius: max(20px, 1.3889vw) 0 0 max(20px, 1.3889vw);
}


.package-image {
  display: block;
  width: 100%;
  height: 100%;
  object-fit: cover;
  transition: transform 0.35s ease;
}

.package-image-box:hover .package-image {
  transform: scale(1.035);
}

.package-image-box:focus-visible {
  outline: max(3px, 0.2083vw) solid #2d7bd6;
  outline-offset: min(-3px, -0.2083vw);
}

.package-tag {
  position: absolute;
  bottom: max(14px, 0.9722vw);
  left: max(14px, 0.9722vw);
  padding: max(6px, 0.4167vw) max(14px, 0.9722vw);
  border-radius: max(20px, 1.3889vw);
  background: rgba(68, 68, 68, 0.55);
  color: #ffffff;
  font-size: max(12px, 0.8333vw);
  font-weight: 500;
  backdrop-filter: blur(max(6px, 0.4167vw));
}
.package-details {
  display: flex;
  flex-direction: column;
  min-width: 0;
  height: max(342px, 23.75vw);
  padding: max(28px, 1.9444vw) max(30px, 2.0833vw);
}

.package-title {
  display: flex;
  align-items: center;
  justify-content: space-between;
  gap: max(12px, 0.8333vw);
  margin-bottom: max(18px, 1.25vw);
}

.package-title-main {
  display: flex;
  align-items: center;
  flex-wrap: wrap;
  gap: max(12px, 0.8333vw);
  min-width: 0;
}

.badge {
  display: inline-flex;
  align-items: center;
  justify-content: center;
  height: max(28px, 1.9444vw);
  padding: 0 max(16px, 1.1111vw);
  background: #eef3fa;
  color: #1a51ad;
  font-size: max(11px, 0.7639vw);
  font-weight: 600;
  letter-spacing: max(1px, 0.0694vw);
  text-transform: uppercase;
  border-radius: max(4px, 0.2778vw);
  white-space: nowrap;
}

.package-title h3 {
  color: var(--color-pine);
  font-size: max(26px, 1.8056vw);
  font-weight: 500;
}

.rating-badge {
  display: inline-flex;
  align-items: center;
  gap: max(5px, 0.3472vw);
  padding: max(6px, 0.4167vw) max(9px, 0.625vw);
  border-radius: max(8px, 0.5556vw);
  background: #fff8e8;
  color: #5e4a18;
  font-size: max(12px, 0.8333vw);
  font-weight: 700;
}

.package-description {
  margin-bottom: max(18px, 1.25vw);
  color: var(--color-ink-soft);
  font-size: max(13.5px, 0.9375vw);
  line-height: 1.6;
}

.package-meta {
  display: flex;
  flex-wrap: wrap;
  gap: max(18px, 1.25vw);
  margin-bottom: max(24px, 1.6667vw);
  font-size: max(12px, 0.8333vw);
  font-weight: 600;
}

.package-meta span,
.include-item {
  display: inline-flex;
  align-items: center;
  gap: max(7px, 0.4861vw);
}

.package-meta i {
  color: #1a51ad;
  font-size: max(12px, 0.8333vw);
}

.rating-badge i {
  color: #d79b14;
}

.options-row {
  display: flex;
  flex-direction: column;
  gap: max(20px, 1.3889vw);
  margin-top: max(24px, 1.6667vw);
}

.option label {
  display: block;
  margin-bottom: max(10px, 0.6944vw);
  color: var(--color-ink-soft);
  font-size: max(12.5px, 0.8681vw);
  font-weight: 600;
  letter-spacing: max(0.4px, 0.0278vw);
  text-transform: uppercase;
}

.includes-card {
  display: flex;
  align-items: center;
  gap: max(14px, 0.9722vw);
  padding: max(12px, 0.8333vw) max(16px, 1.1111vw);
  border: max(1px, 0.0694vw) solid #e7ebf0;
  border-radius: max(12px, 0.8333vw);
  background: #f8f9fb;
}

.includes-list {
  display: flex;
  align-items: center;
  flex: 1;
  min-width: 0;
  gap: max(20px, 1.3889vw);
  overflow: hidden;
}

.include-item {
  display: inline-flex;
  align-items: center;
  flex-shrink: 0;
  gap: max(7px, 0.4861vw);
  font-size: max(11px, 0.7639vw);
  white-space: nowrap;
}

.include-item i {
  color: var(--color-pine);
  font-size: max(11px, 0.7639vw);
}

.see-more-btn {
  flex-shrink: 0;
  margin-left: auto;
  padding: 0;
  border: 0;
  background: transparent;
  color: var(--color-pine);
  font-size: max(11px, 0.7639vw);
  font-weight: 600;
  white-space: nowrap;
  cursor: pointer;
}

.see-more-btn:hover {
  text-decoration: underline;
}

.divider {
  border-left: max(1px, 0.0694vw) dashed var(--color-line);
  margin: max(24px, 1.6667vw) 0;
}

.package-options {
  display: flex;
  flex-direction: column;
  justify-content: center;
  padding: max(28px, 1.9444vw) max(28px, 1.9444vw) max(28px, 1.9444vw) max(4px, 0.2778vw);
}

.price-label {
  margin-bottom: max(6px, 0.4167vw);
  color: var(--color-ink-soft);
  font-size: max(12px, 0.8333vw);
  font-weight: 600;
  letter-spacing: max(0.4px, 0.0278vw);
  text-transform: uppercase;
}

.price-row {
  display: flex;
  align-items: baseline;
  gap: max(8px, 0.5556vw);
  flex-wrap: wrap;
}

.price {
  color: var(--color-pine);
  font-size: max(30px, 2.0833vw);
  font-weight: 600;
}

.price-caption {
  color: var(--color-ink-soft);
  font-size: max(11.5px, 0.7986vw);
}

.price-breakdown {
  margin-top: max(4px, 0.2778vw);
  color: #413f3f;
  font-size: max(12px, 0.8333vw);
}

.select-package-btn {
  width: 100%;
  height: max(46px, 3.1944vw);
  border-radius: max(15px, 1.0417vw);
  font: inherit;
  font-size: max(14px, 0.9722vw);
  font-weight: 500;
  cursor: pointer;
}

.select-package-btn {
  margin-top: max(16px, 1.1111vw);
  border: 0;
  background: #021c44;
  color: #ffffff;
}


.details-overlay {
  position: fixed;
  inset: 0;
  z-index: 100000;
  display: grid;
  place-items: center;
  padding: max(20px, 1.3889vw);
  background: rgba(5, 19, 40, 0.66);
}

.details-dialog {
  position: relative;
  width: min(100%, max(760px, 52.7778vw));
  max-height: calc(100vh - max(40px, 2.7778vw));
  overflow: auto;
  border-radius: max(20px, 1.3889vw);
  background: #ffffff;
}

.details-dialog > img {
  width: 100%;
  height: max(280px, 19.4444vw);
  object-fit: cover;
}

.close-btn {
  position: absolute;
  top: max(14px, 0.9722vw);
  right: max(14px, 0.9722vw);
  width: max(40px, 2.7778vw);
  height: max(40px, 2.7778vw);
  border: 0;
  border-radius: 50%;
  background: #ffffff;
  cursor: pointer;
}

.dialog-content {
  padding: max(28px, 1.9444vw);
}

.dialog-content > span {
  color: #2476c8;
  font-size: max(11px, 0.7639vw);
  font-weight: 800;
}

.dialog-content h2 {
  margin: max(7px, 0.4861vw) 0;
  color: #123f79;
  font-size: max(30px, 2.0833vw);
}

.dialog-content p {
  color: #657286;
  line-height: 1.7;
}

.dialog-content ul {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: max(10px, 0.6944vw);
  margin-top: max(20px, 1.3889vw);
  list-style: none;
}

.dialog-content li {
  display: flex;
  gap: max(8px, 0.5556vw);
  color: #566273;
  font-size: max(13px, 0.9028vw);
}

.dialog-footer {
  display: flex;
  align-items: center;
  justify-content: space-between;
  margin-top: max(24px, 1.6667vw);
  padding-top: max(20px, 1.3889vw);
  border-top: max(1px, 0.0694vw) solid #e7ecf2;
}

.dialog-footer strong {
  color: #123f79;
  font-size: max(30px, 2.0833vw);
}

.dialog-footer button {
  min-height: max(46px, 3.1944vw);
  padding: 0 max(22px, 1.5278vw);
  border: 0;
  border-radius: max(10px, 0.6944vw);
  background: #174d91;
  color: #ffffff;
  font-weight: 700;
  cursor: pointer;
}

@media (max-width: 1200px) {
  .packages-section {
    padding: max(50px, 3.4722vw) max(30px, 2.0833vw);
  }

  .package-card {
    grid-template-columns: max(260px, 18.0556vw) 1fr max(24px, 1.6667vw) max(220px, 15.2778vw);
  }

  .package-details {
    padding: max(26px, 1.8056vw) max(24px, 1.6667vw) max(26px, 1.8056vw) max(22px, 1.5278vw);
  }
}

@media (max-width: 1050px) {
  .package-card {
    grid-template-columns: max(260px, 18.0556vw) 1fr;
  }

  .divider {
    display: none;
  }

  .package-options {
    grid-column: 1 / -1;
    margin: max(20px, 1.3889vw) max(28px, 1.9444vw) 0;
    padding: max(24px, 1.6667vw) 0 max(28px, 1.9444vw);
    border-top: max(1px, 0.0694vw) dashed var(--color-line);
  }
}

@media (max-width: 768px) {
  .package-card {
    grid-template-columns: 1fr;
  }

  .package-image-box {
    width: 100%;
    height: max(220px, 15.2778vw);
    border-radius: max(20px, 1.3889vw) max(20px, 1.3889vw) 0 0;
  }

  .package-details {
    height: auto;
    padding: max(24px, 1.6667vw);
  }

  .includes-card {
    align-items: flex-start;
  }

  .includes-list {
    flex-wrap: wrap;
    overflow: visible;
  }

  .packages-section h2 {
    font-size: max(26px, 1.8056vw);
  }

  .package-options {
    margin: max(20px, 1.3889vw) max(24px, 1.6667vw) 0;
    padding: max(32px, 2.2222vw) 0 max(24px, 1.6667vw);
  }

  .dialog-content ul {
    grid-template-columns: 1fr;
  }
}

@media (max-width: 480px) {
  .packages-section {
    padding: max(40px, 2.7778vw) max(16px, 1.1111vw);
  }

  .packages-section h2 {
    font-size: max(22px, 1.5278vw);
  }

  .package-title h3 {
    font-size: max(21px, 1.4583vw);
  }

  .price {
    font-size: max(26px, 1.8056vw);
  }
}

</style>
