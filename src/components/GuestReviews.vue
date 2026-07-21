<template>
  <section class="reviews-section">
    <div class="container">

      <!-- Title -->
      <div class="title">
        <div class="eyebrow">
          04 — GUEST REVIEWS
        </div>

        <div class="heading">
          <h2>What our guests are saying</h2>
        </div>
      </div>

      <!-- Review Summary -->
      <div class="summary">

        <div class="score-row">
          <span class="badge">7.9</span>

          <p>
            <strong>Good</strong> · 936 reviews
            <a href="#"
              @click.prevent="showReviewsModal = 
              true">
              Read all reviews</a>
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

          <button
            v-for="topic in topics"
            :key="topic"
          >
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
  {
    name: "Facilities",
    score: 8.1,
  },
  {
    name: "Value for money",
    score: 8.5,
  },
  {
    name: "Staff",
    score: 8.5,
  },
]

const rightRatings = [
  {
    name: "Cleanliness",
    score: 8.4,
  },
  {
    name: "Location",
    score: 8.1,
  },
  {
    name: "Free Wifi",
    score: 8.4,
  },
]

const topics = [
  "Room",
  "Clean",
  "Location",
  "Bathroom",
  "Bed",
]

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

*{
  margin:0;
  padding:0;
  box-sizing:border-box;
  font-family: "Figtree", sans-serif;
}

.reviews-section{
    padding:50px 0;
}

.eyebrow{
    color:#034acf;
    font-size:10px;
    font-weight:700;
    letter-spacing:1px;
    margin-bottom:10px;
}

.heading{
    display:flex;
    justify-content:space-between;
    align-items:flex-end;
    gap:30px;
}

.heading h2{
    font-size:34px;
    font-weight:500;
    color:#1A51AD;
    margin-bottom:30px;
    line-height:1.3;
}

/* SUMMARY */

.summary{
    margin-top: 0px;
}

.score-row{
    display:flex;
    align-items:center;
    gap:18px;
    margin-bottom:45px;
}

.badge{
    width:32px;
    height:32px;
    border-radius:8px;
    background:#011f42;
    color:#fff;
    display:flex;
    justify-content:center;
    align-items:center;
    font-weight:650;
    font-size:12px;
}

.score-row p{
    color:#555;
    font-size: 12px;
}

.score-row a{
    margin-left:8px;
    color:#1565c0;
    text-decoration:none;
    font-weight:600;
}

.section-title{
    font-size:25px;
    margin-bottom:35px;
    color:#031b35;
}

/* RATINGS */

.ratings-grid{
    display:grid;
    grid-template-columns:220px 1fr 1fr;
    gap:60px;
    align-items:start;
}

.circle {
  width: 180px;
  height: 180px;
  border-radius: 50%;
  position: relative;

  background: conic-gradient(
    #001a37 0deg 285deg,   /* colored part */
    #d3dcee 285deg 360deg  /* remaining part */
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
}

.rating-item{
    margin-bottom:28px;
}

.label{
    display:flex;
    justify-content:space-between;
    margin-bottom:10px;
    color:#444;
}

.progress{
    height:8px;
    background:#ececec;
    border-radius:20px;
    overflow:hidden;
}

.fill{
    height:100%;
    background:#073C94;
    border-radius:20px;
}

/* TOPICS */

.topics-section{
    margin-top:70px;
}

.topics-section h3{
    font-size:25px;
    margin-bottom:30px;
    color: #031b35;
}

.topics{
    display:flex;
    flex-wrap:wrap;
    gap:14px;
}

.topics button{
    padding:8px 20px;
    border-radius:30px;
    background:#fff;
    border:1px solid #d8d8d8;
    cursor:pointer;
    transition:.3s;
    font-size:15px;
}

.topics button:hover{
    background:#031b35;
    color:white;
}

/* REVIEWS */

.reviews-section-cards{
    margin-top:70px;
}

.love-title{
    font-size:25px;
    margin-bottom:30px;
    color: #031b35;
}

.reviews-grid{
    display:grid;
    grid-template-columns:repeat(3,minmax(0,1fr));
    gap:28px;
}

.review-card{
    background:white;
    border-radius:16px;
    border:1px solid #ececec;
    padding:28px;
    min-height:300px;
    box-shadow:0 6px 20px rgba(0,0,0,.05);
    transition:.3s;
}

.review-card:hover{
    transform:translateY(-5px);
    box-shadow:0 14px 35px rgba(0,0,0,.12);
}

.review-header{
    display:flex;
    gap:15px;
    align-items:flex-start;
    margin-bottom:20px;
}

.avatar{
    width:48px;
    height:48px;
    border-radius:50%;
    background:#1565c0;
    color:white;
    display:flex;
    justify-content:center;
    align-items:center;
    font-size:18px;
    font-weight:700;
    flex-shrink:0;
}

.review-info h4{
    margin:0;
    font-size:18px;
}

.review-info small{
    display:block;
    color:#777;
    margin:4px 0;
}

.review-info p{
    margin:0;
    color:#666;
}

.review-text{
    color:#555;
    line-height:1.8;
    margin-bottom:20px;
}

.review-card a{
    color:#1565c0;
    text-decoration:none;
    font-weight:600;
}

.review-card a:hover{
    text-decoration:underline;
}

.read-all{
    margin-top:45px;
    background:#3B3B3B;
    color:white;
    border:none;
    padding:15px 28px;
    border-radius:8px;
    cursor:pointer;
    transition:.3s;
    font-size: 12px;
}

.read-all:hover{
    background:#282727;
}

/* RESPONSIVE */

@media(max-width:1100px){

    .ratings-grid{
        grid-template-columns:1fr;
    }

    .circle{
        margin:auto;
    }

    .reviews-grid{
        grid-template-columns:1fr 1fr;
    }

}

@media(max-width:768px){

    .heading{
        flex-direction:column;
        align-items:flex-start;
    }

    .heading h2{
        font-size:42px;
    }

    .reviews-grid{
        grid-template-columns:1fr;
    }

    .circle{
        width:180px;
        height:180px;
    }

    .circle-content h1{
        font-size:58px;
    }

}
</style>

