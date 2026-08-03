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

            <a href="#" @click.prevent="showReviewsModal = true">Read more</a>
          </div>
        </div>
      </div>

      <button class="read-all" @click="showReviewsModal = true">
        Read all reviews
      </button>

    </div>
  </section>

  <ReviewsModal
    :show="showReviewsModal"
    :reviews="reviews"
    :left-ratings="leftRatings"
    :right-ratings="rightRatings"
    :room-options="roomOptions"
    @close="showReviewsModal = false"
  />
</template>

<script setup>
import { ref } from "vue"
import ReviewsModal from "./modals/ReviewsModal.vue"

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

const roomOptions = ["Panoramic Deluxe", "Green Zone Deluxe"]

const reviews = [
  {
    initial: "N",
    name: "Naveen",
    reviewCount: "7 reviews",
    flag: "🇱🇰",
    country: "Sri Lanka",
    text:
      "The accommodation was comfortable, the facilities were well maintained, and the restaurant served fresh, tasty meals. We would definitely recommend this resort to anyone looking for a peaceful getaway.",
    title: "A peaceful, well-maintained getaway",
    date: "March 2026",
    room: "Panoramic Deluxe",
    ratings: [
      { name: "Cleanliness", score: 8.5 },
      { name: "Staff", score: 9.0 },
      { name: "Facilities", score: 8.0 },
      { name: "Value for money", score: 8.5 },
    ],
    response: {
      date: "March 2026",
      text: "Thank you so much for your kind words, Naveen! We're delighted you enjoyed your stay and hope to welcome you back soon.",
      staff: "Priya Fernando",
      role: "Guest Relations Manager",
    },
  },
  {
    initial: "J",
    name: "Jane",
    reviewCount: "7 reviews",
    flag: "🇮🇳",
    country: "India",
    text:
      "The resort exceeded our expectations. Everyone was kind, professional and genuinely cared about making our stay enjoyable. The room was spacious, clean and well equipped.",
    title: "Exceeded our expectations",
    date: "February 2026",
    room: "Green Zone Deluxe",
    ratings: [
      { name: "Cleanliness", score: 9.0 },
      { name: "Staff", score: 9.5 },
      { name: "Facilities", score: 8.5 },
      { name: "Location", score: 8.0 },
    ],
    response: {
      date: "February 2026",
      text: "It means a lot to hear this, Jane — thank you for taking the time to share it. We hope to see your family again soon!",
      staff: "Priya Fernando",
      role: "Guest Relations Manager",
    },
  },
  {
    initial: "D",
    name: "David",
    reviewCount: "7 reviews",
    flag: "🇺🇸",
    country: "United States",
    text:
      "We had a wonderful stay from start to finish. The staff were incredibly welcoming, our room was spotless and the peaceful surroundings made it easy to relax.",
    title: "Wonderful from start to finish",
    date: "January 2026",
    room: "Panoramic Deluxe",
    ratings: [
      { name: "Cleanliness", score: 9.0 },
      { name: "Staff", score: 9.0 },
      { name: "Free Wifi", score: 8.0 },
      { name: "Value for money", score: 8.5 },
    ],
    response: {
      date: "January 2026",
      text: "Thank you, David! We're so glad the peaceful surroundings gave you the relaxing stay you were looking for.",
      staff: "Priya Fernando",
      role: "Guest Relations Manager",
    },
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

  padding: max(50px, 3.4722vw) 0;
  background: var(--color-bg);
  background: #ffffff;
}

.container {
  max-width: max(1200px, 83.3333vw);
  margin: 0 auto;
  padding: 0 max(20px, 1.3889vw);
}

/* SECTION HEAD */

.section-head {
  margin-bottom: max(56px, 3.8889vw);
}
.eyebrow {
  color: #034acf;
  font-size: clamp(max(10px, 0.6944vw), 0.8vw, max(12px, 0.8333vw));
  font-weight: 700;
  letter-spacing: max(1px, 0.0694vw);
  margin-bottom: clamp(max(8px, 0.5556vw), 1vw, max(12px, 0.8333vw));
}

.section-head h2{
  font-size:max(34px, 2.3611vw);
  font-weight:500;
  color:#1A51AD;
  margin-bottom: max(80px, 5.5556vw);
  line-height:1.3;
}

.subhead {
  margin-bottom: min(-30px, -2.0833vw);
  font-size: max(15px, 1.0417vw);
  color: var(--color-ink-soft);
}

/* SUMMARY */

.summary {
  margin-top: 0;
}

.score-row {
  display: flex;
  align-items: center;
  gap: max(18px, 1.25vw);
  margin-bottom: max(45px, 3.125vw);
}

.badge {
  width: max(32px, 2.2222vw);
  height: max(32px, 2.2222vw);
  border-radius: max(8px, 0.5556vw);
  background: var(--color-blue-dark);
  color: #fff;
  display: flex;
  justify-content: center;
  align-items: center;
  font-weight: 650;
  font-size: max(12px, 0.8333vw);
}

.score-row p {
  color: var(--color-ink-soft);
  font-size: max(12px, 0.8333vw);
}

.score-row a {
  margin-left: max(8px, 0.5556vw);
  color: var(--color-blue-light);
  text-decoration: none;
  font-weight: 600;
  cursor: pointer;
}

.score-row a:hover {
  text-decoration: underline;
}

.section-title {
  font-size: max(25px, 1.7361vw);
  margin-bottom: max(30px, 2.0833vw);
  font-weight: 550;
  color: var(--color-ink);
}

/* RATINGS */

.ratings-grid {
  display: grid;
  grid-template-columns: max(220px, 15.2778vw) 1fr 1fr;
  gap: max(60px, 4.1667vw);
  align-items: start;
}

.circle {
  width: max(180px, 12.5vw);
  height: max(180px, 12.5vw);
  border-radius: 50%;
  position: relative;

  background: conic-gradient(
    var(--color-blue-dark) 0deg 285deg,
    var(--color-line) 285deg 360deg
  );

  display: flex;
  justify-content: center;
  align-items: center;
  font-size: max(20px, 1.3889vw);
}

.circle::before {
  content: "";
  position: absolute;
  inset: max(10px, 0.6944vw);
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
  margin-bottom: max(28px, 1.9444vw);
}

.label {
  display: flex;
  justify-content: space-between;
  margin-bottom: max(10px, 0.6944vw);
  color: var(--color-ink);
}

.progress {
  height: max(8px, 0.5556vw);
  background: var(--color-line);
  border-radius: max(20px, 1.3889vw);
  overflow: hidden;
}

.fill {
  height: 100%;
  background: var(--color-blue-dark);
  border-radius: max(20px, 1.3889vw);
}

/* TOPICS */

.topics-section {
  margin-top: max(30px, 2.0833vw);
}

.topics-section h3 {
  font-size: max(25px, 1.7361vw);
  margin-bottom: max(20px, 1.3889vw);
  font-weight: 550;
  color: var(--color-ink);
}

.topics {
  display: flex;
  flex-wrap: wrap;
  gap: max(14px, 0.9722vw);
}

.topics button {
  padding: max(8px, 0.5556vw) max(20px, 1.3889vw);
  border-radius: max(30px, 2.0833vw);
  background: #fff;
  border: max(1px, 0.0694vw) solid var(--color-line);
  cursor: pointer;
  transition: .3s;
  font-size: max(15px, 1.0417vw);
  color: var(--color-ink);
}

.topics button:hover {
  background: var(--color-blue-dark);
  border-color: var(--color-blue-dark);
  color: white;
}

/* REVIEWS */

.reviews-section-cards {
  margin-top: max(40px, 2.7778vw);
}

.love-title {
  font-size: max(25px, 1.7361vw);
  margin-bottom: max(20px, 1.3889vw);
  font-weight: 550;
  color: var(--color-ink);
}

.reviews-grid {
  display: grid;
  grid-template-columns: repeat(3, minmax(0, 1fr));
  gap: max(28px, 1.9444vw);
}

.review-card {
  background: white;
  border-radius: max(16px, 1.1111vw);
  border: max(1px, 0.0694vw) solid var(--color-line);
  padding: max(28px, 1.9444vw);
  min-height: max(300px, 20.8333vw);
  box-shadow: 0 max(6px, 0.4167vw) max(20px, 1.3889vw) rgba(0, 0, 0, .05);
  transition: .3s;
}

.review-card:hover {
  transform: translateY(min(-5px, -0.3472vw));
  box-shadow: 0 max(14px, 0.9722vw) max(35px, 2.4306vw) rgba(0, 0, 0, .12);
}

.review-header {
  display: flex;
  gap: max(15px, 1.0417vw);
  align-items: flex-start;
  margin-bottom: max(20px, 1.3889vw);
}

.avatar {
  width: max(48px, 3.3333vw);
  height: max(48px, 3.3333vw);
  border-radius: 50%;
  background: var(--color-blue-light);
  color: white;
  display: flex;
  justify-content: center;
  align-items: center;
  font-size: max(18px, 1.25vw);
  font-weight: 700;
  flex-shrink: 0;
}

.review-info h4 {
  margin: 0;
  font-size: max(18px, 1.25vw);
  color: var(--color-ink);
}

.review-info small {
  display: block;
  color: var(--color-ink-soft);
  margin: max(4px, 0.2778vw) 0;
}

.review-info p {
  margin: 0;
  color: var(--color-ink-soft);
}

.review-text {
  color: var(--color-ink-soft);
  line-height: 1.8;
  margin-bottom: max(20px, 1.3889vw);
}

.review-card a {
  color: var(--color-blue-light);
  text-decoration: none;
  font-weight: 600;
  cursor: pointer;
}

.review-card a:hover {
  text-decoration: underline;
}

.read-all {
  margin-top: max(45px, 3.125vw);
  background: #3B3B3B;
  color: white;
  border: none;
  padding: max(15px, 1.0417vw) max(28px, 1.9444vw);
  border-radius: max(8px, 0.5556vw);
  cursor: pointer;
  transition: .3s;
  font-size: max(12px, 0.8333vw);
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