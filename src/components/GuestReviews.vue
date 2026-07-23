<template>
  <section class="reviews-section">
    <div class="container">

      <header class="section-head">
        <div class="eyebrow">
           — GUEST REVIEWS
        </div>

        <h2>What our guests are saying</h2>

        <p class="subhead">A closer look at what people loved about their stay</p>
      </header>

      <!-- Review Summary -->
      <div class="summary">

        <div class="score-row">
          <span class="badge">7.9</span>

          <p>
            <strong>Good</strong> · 936 reviews
            <a href="#" @click.prevent="showReviewsModal = true">
              Read all reviews
            </a>
          </p>
        </div>

        <h3 class="section-title">
          Reviews by Categories:
        </h3>

        <div class="ratings-grid">

          <!-- Circle -->
          <div class="circle">
            <div class="circle-content">
              <h1>7.9</h1>
              <span>/10</span>
            </div>
          </div>

          <!-- Left Ratings -->
          <div class="bars">
            <div
              class="rating-item"
              v-for="item in leftRatings"
              :key="item.name"
            >
              <div class="label">
                <span>{{ item.name }}</span>
                <span>{{ item.score }}</span>
              </div>

              <div class="progress">
                <div
                  class="fill"
                  :style="{ width: item.score * 10 + '%' }"
                ></div>
              </div>
            </div>
          </div>

          <!-- Right Ratings -->
          <div class="bars">
            <div
              class="rating-item"
              v-for="item in rightRatings"
              :key="item.name"
            >
              <div class="label">
                <span>{{ item.name }}</span>
                <span>{{ item.score }}</span>
              </div>

              <div class="progress">
                <div
                  class="fill"
                  :style="{ width: item.score * 10 + '%' }"
                ></div>
              </div>
            </div>
          </div>

        </div>

      </div>

      <!-- Topics -->
      <div class="topics-section">
        <h3>Select topics to read reviews:</h3>

        <div class="topics">
          <button v-for="topic in topics" :key="topic">
            + {{ topic }}
          </button>
        </div>
      </div>

      <!-- Review Cards -->
      <div class="reviews-section-cards">
        <h3 class="love-title">
          See what guests loved the most:
        </h3>

        <div class="reviews-grid">
          <div
            class="review-card"
            v-for="review in reviews"
            :key="review.name"
          >
            <div class="review-header">
              <div class="avatar">
                {{ review.initial }}
              </div>

              <div class="review-info">
                <h4>{{ review.name }}</h4>
                <small>{{ review.reviewCount }}</small>
                <p>{{ review.flag }} {{ review.country }}</p>
              </div>
            </div>

            <p class="review-text">
              "{{ review.text }}"
            </p>

            <a href="#">Read more</a>
          </div>
        </div>
      </div>

      <button class="read-all">
        Read all reviews
      </button>

    </div>
  </section>

  <ReviewsModal
    :show="showReviewsModal"
    :reviews="reviews"
    :left-ratings="leftRatings"
    :right-ratings="rightRatings"
    @close="showReviewsModal = false"
  />
</template>

<script setup>
import { ref } from "vue"
import ReviewsModal from "./ReviewsModal.vue"

const showReviewsModal = ref(false)

const leftRatings = [
  { name: "Facilities", score: 8.1 },
  { name: "Value for money", score: 8.5 },
  { name: "Staff", score: 8.5 },
]

const rightRatings = [
  { name: "Cleanliness", score: 8.4 },
  { name: "Location", score: 8.1 },
  { name: "Free Wifi", score: 8.4 },
]

const topics = ["Room", "Clean", "Location", "Bathroom", "Bed"]

const reviews = [
  {
    initial: "N",
    name: "Naveen",
    reviewCount: "7 reviews",
    flag: "🇱🇰",
    country: "Sri Lanka",
    text:
      "The accommodation was comfortable, the facilities were well maintained, and the restaurant served fresh, tasty meals. We would definitely recommend this resort to anyone looking for a peaceful getaway.",
  },
  {
    initial: "J",
    name: "Jane",
    reviewCount: "7 reviews",
    flag: "🇮🇳",
    country: "India",
    text:
      "The resort exceeded our expectations. Everyone was kind, professional and genuinely cared about making our stay enjoyable. The room was spacious, clean and well equipped.",
  },
  {
    initial: "D",
    name: "David",
    reviewCount: "7 reviews",
    flag: "🇺🇸",
    country: "United States",
    text:
      "We had a wonderful stay from start to finish. The staff were incredibly welcoming, our room was spotless and the peaceful surroundings made it easy to relax.",
  },
]
</script>

<style scoped>

* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
  font-family: 'Figtree';
}

.reviews-section {
  --color-bg: #eef4fa;
  --color-ink: #2b2f36;
  --color-ink-soft: #6b7280;
  --color-line: #dce6f0;
  --color-blue-dark: #19509d;
  --color-blue-light: #2a6bb0;

  padding: 50px 0;
  background: var(--color-bg);
  background: #ffffff;
}

.container {
  max-width: 1200px;
  margin: 0 auto;
  padding: 0 20px;
}

/* SECTION HEAD */

.eyebrow {
  color: #034acf;
  font-size: 10px;
  font-weight: 700;
  letter-spacing: 1px;
  margin-bottom: 10px;
}

.section-head {
  margin-bottom: 56px;
}

.section-head h2 {
  font-size: 34px;
  font-weight: 500;
  color: #1A51AD;
  margin-bottom: 55px;
  line-height: 1.3;
}

.subhead {
  margin-bottom: -30px;
  font-size: 15px;
  color: var(--color-ink-soft);
}

/* SUMMARY */

.summary {
  margin-top: 0;
}

.score-row {
  display: flex;
  align-items: center;
  gap: 18px;
  margin-bottom: 45px;
}

.badge {
  width: 32px;
  height: 32px;
  border-radius: 8px;
  background: var(--color-blue-dark);
  color: #fff;
  display: flex;
  justify-content: center;
  align-items: center;
  font-weight: 650;
  font-size: 12px;
}

.score-row p {
  color: var(--color-ink-soft);
  font-size: 12px;
}

.score-row a {
  margin-left: 8px;
  color: var(--color-blue-light);
  text-decoration: none;
  font-weight: 600;
}

.score-row a:hover {
  text-decoration: underline;
}

.section-title {
  font-size: 25px;
  margin-bottom: 30px;
  font-weight: 550;
  color: var(--color-ink);
}

/* RATINGS */

.ratings-grid {
  display: grid;
  grid-template-columns: 220px 1fr 1fr;
  gap: 60px;
  align-items: start;
}

.circle {
  width: 180px;
  height: 180px;
  border-radius: 50%;
  position: relative;

  background: conic-gradient(
    var(--color-blue-dark) 0deg 285deg,
    var(--color-line) 285deg 360deg
  );

  display: flex;
  justify-content: center;
  align-items: center;
  font-size: 20px;
}

.circle::before {
  content: "";
  position: absolute;
  inset: 10px;
  border-radius: 50%;
  background: white;
}

.circle-content {
  position: relative;
  z-index: 1;
  text-align: center;
  color: var(--color-ink);
}

.rating-item {
  margin-bottom: 28px;
}

.label {
  display: flex;
  justify-content: space-between;
  margin-bottom: 10px;
  color: var(--color-ink);
}

.progress {
  height: 8px;
  background: var(--color-line);
  border-radius: 20px;
  overflow: hidden;
}

.fill {
  height: 100%;
  background: var(--color-blue-dark);
  border-radius: 20px;
}

/* TOPICS */

.topics-section {
  margin-top: 30px;
}

.topics-section h3 {
  font-size: 25px;
  margin-bottom: 20px;
  font-weight: 550;
  color: var(--color-ink);
}

.topics {
  display: flex;
  flex-wrap: wrap;
  gap: 14px;
}

.topics button {
  padding: 8px 20px;
  border-radius: 30px;
  background: #fff;
  border: 1px solid var(--color-line);
  cursor: pointer;
  transition: .3s;
  font-size: 15px;
  color: var(--color-ink);
}

.topics button:hover {
  background: var(--color-blue-dark);
  border-color: var(--color-blue-dark);
  color: white;
}

/* REVIEWS */

.reviews-section-cards {
  margin-top: 40px;
}

.love-title {
  font-size: 25px;
  margin-bottom: 20px;
  font-weight: 550;
  color: var(--color-ink);
}

.reviews-grid {
  display: grid;
  grid-template-columns: repeat(3, minmax(0, 1fr));
  gap: 28px;
}

.review-card {
  background: white;
  border-radius: 16px;
  border: 1px solid var(--color-line);
  padding: 28px;
  min-height: 300px;
  box-shadow: 0 6px 20px rgba(0, 0, 0, .05);
  transition: .3s;
}

.review-card:hover {
  transform: translateY(-5px);
  box-shadow: 0 14px 35px rgba(0, 0, 0, .12);
}

.review-header {
  display: flex;
  gap: 15px;
  align-items: flex-start;
  margin-bottom: 20px;
}

.avatar {
  width: 48px;
  height: 48px;
  border-radius: 50%;
  background: var(--color-blue-light);
  color: white;
  display: flex;
  justify-content: center;
  align-items: center;
  font-size: 18px;
  font-weight: 700;
  flex-shrink: 0;
}

.review-info h4 {
  margin: 0;
  font-size: 18px;
  color: var(--color-ink);
}

.review-info small {
  display: block;
  color: var(--color-ink-soft);
  margin: 4px 0;
}

.review-info p {
  margin: 0;
  color: var(--color-ink-soft);
}

.review-text {
  color: var(--color-ink-soft);
  line-height: 1.8;
  margin-bottom: 20px;
}

.review-card a {
  color: var(--color-blue-light);
  text-decoration: none;
  font-weight: 600;
}

.review-card a:hover {
  text-decoration: underline;
}

.read-all {
  margin-top: 45px;
  background: #3B3B3B;
  color: white;
  border: none;
  padding: 15px 28px;
  border-radius: 8px;
  cursor: pointer;
  transition: .3s;
  font-size: 12px;
}

.read-all:hover {
  background: #282727;
}

/* RESPONSIVE — aligned to the same 992 / 768 breakpoints PoliciesSection uses */

@media (max-width: 992px) {
  .ratings-grid {
    grid-template-columns: 1fr;
  }

  .circle {
    margin: auto;
  }

  .reviews-grid {
    grid-template-columns: 1fr 1fr;
  }
}

@media (max-width: 768px) {
  .reviews-section {
    padding: 48px 16px;
  }

  .section-head h2 {
    font-size: 32px;
  }

  .reviews-grid {
    grid-template-columns: 1fr;
  }

  .circle {
    width: 180px;
    height: 180px;
  }

  .circle-content h1 {
    font-size: 58px;
  }
}
</style>