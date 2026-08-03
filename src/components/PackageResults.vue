<template>
  <section class="packages-section">
    <div class="section-header">
      <div class="eyebrow">03 — PACKAGES</div>
      <h2>Choose your package</h2>

      <div class="bar-wrap">
        <div class="availability-heading">
          <h3>Check Package Availability</h3>

          <span class="price-match">
            <Icon icon="lucide:tag" />
            We Price Match
          </span>
        </div>

        <div class="availability-alert">
          <Icon icon="lucide:circle-alert" />
          <span>Select dates to see available packages and prices</span>
        </div>

        <div class="availability-search">
          <button type="button" class="availability-field">
            <Icon icon="lucide:calendar-days" />
            <span>Check-in date — Check-out date</span>
          </button>

          <button type="button" class="availability-field">
            <Icon icon="lucide:user-round" />
            <span>2 adults · 0 children · 1 room</span>
          </button>

          <button type="button" class="availability-button">
            Search
          </button>
        </div>
      </div>

      <p class="subhead">
        {{ packages.length }} curated packages · accommodation and experiences included
      </p>
    </div>

    <article
      v-for="pkg in packages"
      :key="pkg.id"
      class="package-card"
    >
      <div class="package-image-box">
        <img
          :src="pkg.image"
          :alt="pkg.title"
          class="package-image"
        />
        <span class="package-tag">{{ pkg.category }}</span>
      </div>

      <div class="package-details">
        <div
          v-if="pkg.featured"
          class="offer-badge"
        >
          <Icon icon="lucide:tags" />
          RECOMMENDED
        </div>

        <div class="package-title">
          <h3>{{ pkg.title }}</h3>

          <span class="rating-badge">
            <Icon icon="lucide:star" />
            {{ pkg.rating }}
          </span>
        </div>

        <p class="package-description">
          {{ pkg.description }}
        </p>

        <div class="package-meta">
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

        <div class="options-row">
          <div class="option">
            <label>Package includes</label>

            <div class="includes-card">
              <span
                v-for="item in pkg.includes"
                :key="item"
                class="include-item"
              >
                <Icon icon="lucide:check" />
                {{ item }}
              </span>
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
          @click="emit('checkout-package', pkg)"
        >
          Select this package
        </button>

        <button
          type="button"
          class="details-btn"
          @click="selectedPackage = pkg"
        >
          View package details
          <Icon icon="lucide:arrow-up-right" />
        </button>
      </div>
    </article>

    <Teleport to="body">
      <Transition name="popup">
        <div
          v-if="selectedPackage"
          class="details-overlay"
          @click.self="selectedPackage = null"
        >
          <article class="details-dialog">
            <button
              type="button"
              class="close-btn"
              aria-label="Close package details"
              @click="selectedPackage = null"
            >
              <Icon icon="lucide:x" />
            </button>

            <img
              :src="selectedPackage.image"
              :alt="selectedPackage.title"
            />

            <div class="dialog-content">
              <span>{{ selectedPackage.category }}</span>
              <h2>{{ selectedPackage.title }}</h2>
              <p>{{ selectedPackage.longDescription }}</p>

              <ul>
                <li
                  v-for="item in selectedPackage.highlights"
                  :key="item"
                >
                  <Icon icon="lucide:check" />
                  {{ item }}
                </li>
              </ul>

              <div class="dialog-footer">
                <strong>${{ selectedPackage.price }}</strong>

                <button
                  type="button"
                  @click="bookSelected"
                >
                  Book this package
                </button>
              </div>
            </div>
          </article>
        </div>
      </Transition>
    </Teleport>
  </section>
</template>

<script setup>
import { ref } from "vue";
import { Icon } from "@iconify/vue";

const emit = defineEmits(["checkout-package"]);
const baseUrl = import.meta.env.BASE_URL;
const selectedPackage = ref(null);

const packages = [
  {
    id: 1,
    category: "Nature Escape",
    title: "Signature Nature Stay",
    description:
      "A peaceful stay with breakfast, a guided nature walk and private dining.",
    longDescription:
      "Reconnect with nature through a carefully planned resort experience with comfortable accommodation, breakfast, a guided walk and private dining.",
    image: `${baseUrl}images/img1.jpg`,
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
    category: "Local Experience",
    title: "Coffee Trail & Stay",
    description:
      "A relaxing stay with a guided coffee plantation tour and tasting experience.",
    longDescription:
      "Discover locally grown coffee during a relaxing resort stay with a plantation visit and guided tasting.",
    image: `${baseUrl}images/img3.jpg`,
    rating: "4.8",
    stay: "1 night",
    guests: "2 guests",
    room: "Panoramic Deluxe",
    price: 102,
    featured: false,
    includes: [
      "Daily breakfast",
      "Coffee plantation tour",
      "Coffee tasting"
    ],
    highlights: [
      "Welcome drink",
      "Panoramic Deluxe accommodation",
      "Breakfast for two",
      "Plantation tour",
      "Coffee demonstration",
      "Coffee tasting"
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
    image: `${baseUrl}images/img6.jpg`,
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

function bookSelected() {
  const pkg = { ...selectedPackage.value };
  selectedPackage.value = null;
  emit("checkout-package", pkg);
}
</script>

<style scoped>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
  font-family: "Figtree", sans-serif;
}

.packages-section {
  --color-surface: #ffffff;
  --color-ink: #050505;
  --color-ink-soft: #5d5d5d;
  --color-line: #787c79;
  --color-pine: #073c94;

  max-width: 1340px;
  padding: 50px;
  background: #f8f9fb;
  color: var(--color-ink);
}

.eyebrow {
  margin-bottom: 10px;
  color: #034acf;
  font-size: 10px;
  font-weight: 700;
  letter-spacing: 1px;
}

.packages-section h2 {
  margin-bottom: 55px;
  color: #1a51ad;
  font-size: 34px;
  font-weight: 500;
  line-height: 1.3;
}

.bar-wrap {
  width: 100%;
}

.availability-heading {
  display: flex;
  align-items: center;
  justify-content: space-between;
  margin-bottom: 17px;
}

.availability-heading h3 {
  color: #142238;
  font-size: 20px;
  font-weight: 700;
}

.price-match {
  display: inline-flex;
  align-items: center;
  gap: 7px;
  color: #1273d6;
  font-size: 12px;
  font-weight: 700;
}

.availability-alert {
  display: flex;
  align-items: center;
  gap: 9px;
  min-height: 46px;
  margin-bottom: 18px;
  padding: 0 16px;
  border: 1px solid #f2c9c7;
  border-radius: 9px;
  background: #fff3f2;
  color: #be2622;
  font-size: 12.5px;
}

.availability-search {
  display: grid;
  grid-template-columns: minmax(0, 1.55fr) minmax(0, 1.1fr) 112px;
  gap: 12px;
  margin-bottom: 52px;
}

.availability-field,
.availability-button {
  min-height: 49px;
  border-radius: 9px;
  font: inherit;
}

.availability-field {
  display: flex;
  align-items: center;
  gap: 11px;
  padding: 0 16px;
  border: 1px solid #cfd7e2;
  background: #ffffff;
  color: #303948;
  font-size: 13px;
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
  font-size: 13px;
  font-weight: 700;
  cursor: pointer;
}

.subhead {
  margin-bottom: 20px;
  color: var(--color-ink-soft);
  font-size: 12px;
}

.package-card {
  display: grid;
  grid-template-columns: 300px minmax(0, 1fr) 24px 260px;
  width: 100%;
  min-height: 342px;
  margin-bottom: 28px;
  overflow: hidden;
  border-radius: 20px;
  background: #ffffff;
  box-shadow: 0 1px 2px rgba(31, 46, 36, 0.04);
}


.package-card:hover {
  box-shadow: 0 18px 40px rgba(31, 46, 36, 0.1);
  transform: translateY(-2px);
}

.package-image-box {
  position: relative;
  width: 300px;
  height: 342px;
  min-height: 342px;
  overflow: hidden;
  border-radius: 20px 0 0 20px;
}


.package-image {
  display: block;
  width: 100%;
  height: 100%;
  object-fit: cover;
}

.package-tag {
  position: absolute;
  bottom: 14px;
  left: 14px;
  padding: 6px 14px;
  border-radius: 20px;
  background: rgba(68, 68, 68, 0.55);
  color: #ffffff;
  font-size: 12px;
  font-weight: 500;
  backdrop-filter: blur(6px);
}
.package-details {
  display: flex;
  flex-direction: column;
  min-width: 0;
  height: 342px;
  padding: 28px 30px;
}

.offer-badge {
  display: inline-flex;
  align-items: center;
  gap: 6px;
  margin-bottom: 12px;
  padding: 4px 12px;
  border-radius: 8px;
  background: #eef3fa;
  color: #1a51ad;
  font-size: 10px;
  font-weight: 700;
  letter-spacing: 0.7px;
}

.package-title {
  display: flex;
  align-items: center;
  justify-content: space-between;
  gap: 12px;
  margin-bottom: 6px;
}

.package-title h3 {
  color: var(--color-pine);
  font-size: 26px;
  font-weight: 500;
}

.rating-badge {
  display: inline-flex;
  align-items: center;
  gap: 5px;
  padding: 6px 9px;
  border-radius: 8px;
  background: #fff8e8;
  color: #5e4a18;
  font-size: 12px;
  font-weight: 700;
}

.package-description {
  margin-bottom: 18px;
  color: var(--color-ink-soft);
  font-size: 13.5px;
  line-height: 1.6;
}

.package-meta {
  display: flex;
  flex-wrap: wrap;
  gap: 18px;
  margin-bottom: 24px;
  font-size: 12px;
  font-weight: 600;
}

.package-meta span,
.include-item {
  display: inline-flex;
  align-items: center;
  gap: 7px;
}

.package-meta svg {
  color: #1a51ad;
}

.options-row {
  display: flex;
  flex-direction: column;
  gap: 20px;
  margin-top: 24px;
}

.option label {
  display: block;
  margin-bottom: 10px;
  color: var(--color-ink-soft);
  font-size: 12.5px;
  font-weight: 600;
  letter-spacing: 0.4px;
  text-transform: uppercase;
}

.includes-card {
  display: flex;
  align-items: center;
  flex-wrap: wrap;
  gap: 20px;
  padding: 12px 16px;
  border: 1px solid #e7ebf0;
  border-radius: 12px;
  background: #f8f9fb;
}

.include-item {
  font-size: 11px;
  white-space: nowrap;
}

.include-item svg {
  color: #1d9c53;
}

.divider {
  border-left: 1px dashed var(--color-line);
  margin: 24px 0;
}

.package-options {
  display: flex;
  flex-direction: column;
  justify-content: center;
  padding: 28px 28px 28px 4px;
}

.price-label {
  margin-bottom: 6px;
  color: var(--color-ink-soft);
  font-size: 12px;
  font-weight: 600;
  letter-spacing: 0.4px;
  text-transform: uppercase;
}

.price-row {
  display: flex;
  align-items: baseline;
  gap: 8px;
  flex-wrap: wrap;
}

.price {
  color: var(--color-pine);
  font-size: 30px;
  font-weight: 600;
}

.price-caption {
  color: var(--color-ink-soft);
  font-size: 11.5px;
}

.price-breakdown {
  margin-top: 4px;
  color: #413f3f;
  font-size: 12px;
}

.select-package-btn,
.details-btn {
  width: 100%;
  height: 46px;
  border-radius: 15px;
  font: inherit;
  font-size: 14px;
  font-weight: 500;
  cursor: pointer;
}

.select-package-btn {
  margin-top: 16px;
  border: 0;
  background: #3b3b3b;
  color: #ffffff;
}

.details-btn {
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 7px;
  margin-top: 8px;
  border: 1px solid #dce4ee;
  background: #ffffff;
  color: #1a51ad;
}

.details-overlay {
  position: fixed;
  inset: 0;
  z-index: 100000;
  display: grid;
  place-items: center;
  padding: 20px;
  background: rgba(5, 19, 40, 0.66);
}

.details-dialog {
  position: relative;
  width: min(100%, 760px);
  max-height: calc(100vh - 40px);
  overflow: auto;
  border-radius: 20px;
  background: #ffffff;
}

.details-dialog > img {
  width: 100%;
  height: 280px;
  object-fit: cover;
}

.close-btn {
  position: absolute;
  top: 14px;
  right: 14px;
  width: 40px;
  height: 40px;
  border: 0;
  border-radius: 50%;
  background: #ffffff;
  cursor: pointer;
}

.dialog-content {
  padding: 28px;
}

.dialog-content > span {
  color: #2476c8;
  font-size: 11px;
  font-weight: 800;
}

.dialog-content h2 {
  margin: 7px 0;
  color: #123f79;
  font-size: 30px;
}

.dialog-content p {
  color: #657286;
  line-height: 1.7;
}

.dialog-content ul {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 10px;
  margin-top: 20px;
  list-style: none;
}

.dialog-content li {
  display: flex;
  gap: 8px;
  color: #566273;
  font-size: 13px;
}

.dialog-footer {
  display: flex;
  align-items: center;
  justify-content: space-between;
  margin-top: 24px;
  padding-top: 20px;
  border-top: 1px solid #e7ecf2;
}

.dialog-footer strong {
  color: #123f79;
  font-size: 30px;
}

.dialog-footer button {
  min-height: 46px;
  padding: 0 22px;
  border: 0;
  border-radius: 10px;
  background: #174d91;
  color: #ffffff;
  font-weight: 700;
  cursor: pointer;
}

.popup-enter-active,
.popup-leave-active {
  transition: opacity 0.2s;
}

.popup-enter-from,
.popup-leave-to {
  opacity: 0;
}

@media (max-width: 1200px) {
  .packages-section {
    padding: 50px 30px;
  }

  .package-card {
    grid-template-columns: 260px 1fr 24px 220px;
  }

  .package-details {
    padding: 26px 24px 26px 22px;
  }
}

@media (max-width: 1050px) {
  .package-card {
    grid-template-columns: 260px 1fr;
  }

  .divider {
    display: none;
  }

  .package-options {
    grid-column: 1 / -1;
    margin: 20px 28px 0;
    padding: 24px 0 28px;
    border-top: 1px dashed var(--color-line);
  }
}

@media (max-width: 768px) {
  .availability-search,
  .package-card {
    grid-template-columns: 1fr;
  }

  .package-image-box {
    width: 100%;
    height: 220px;
    border-radius: 20px 20px 0 0;
  }

  .package-details {
    padding: 24px;
  }

  .packages-section h2 {
    font-size: 26px;
  }

  .package-options {
    margin: 20px 24px 0;
    padding: 32px 0 24px;
  }

  .dialog-content ul {
    grid-template-columns: 1fr;
  }
}

@media (max-width: 480px) {
  .packages-section {
    padding: 40px 16px;
  }

  .packages-section h2 {
    font-size: 22px;
  }

  .package-title h3 {
    font-size: 21px;
  }

  .price {
    font-size: 26px;
  }
}
</style>
