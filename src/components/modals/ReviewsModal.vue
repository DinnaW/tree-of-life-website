<template>
  <div
    v-if="show"
    class="modal-overlay"
    @click="$emit('close')"
  >
    <div
      class="modal"
      @click.stop
    >
      <div class="modal-header">
        <h2>Guest Reviews</h2>

        <button
          class="close-btn"
          @click="$emit('close')"
          aria-label="Close reviews"
        >
          <i class="fa-solid fa-xmark"></i>
        </button>
      </div>

      <!-- Body -->
      <div class="modal-body">

        <div class="summary-grid">

          <!-- Rating Circle -->
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

        <!-- STICKY TOOLBAR: title + write review + filters -->
        <div class="sticky-toolbar">
          <div class="toolbar-top">
            <h3 class="all-reviews-title">
              All Reviews ({{ filteredReviews.length }})
            </h3>

            <button type="button" class="write-review-btn" @click="toggleWriteForm">
              <i class="fa-solid" :class="showWriteForm ? 'fa-xmark' : 'fa-pen'"></i>
              {{ showWriteForm ? "Cancel" : "Write a review" }}
            </button>
          </div>

          <div class="review-filters">
            <div class="filters">
              <select>
                <option>Date Range</option>
              </select>
              <select>
                <option>Guest Rating</option>
              </select>
              <select v-model="roomFilter">
                <option value="">Room Type</option>
                <option v-for="room in roomOptions" :key="room" :value="room">{{ room }}</option>
              </select>
            </div>

            <input
              type="text"
              placeholder="Search reviews..."
              class="search-box"
            />
          </div>
        </div>

        <!-- WRITE A REVIEW PANEL -->
        <Transition name="write-form">
          <form v-if="showWriteForm" class="write-review-form" @submit.prevent="submitReview" novalidate>
            <div class="field-row">
              <div class="field">
                <label>Your name</label>
                <input v-model="newReview.name" type="text" placeholder="Enter your name" required />
              </div>
              <div class="field">
                <label>Which room did you stay in?</label>
                <select v-model="newReview.room">
                  <option value="">Select a room</option>
                  <option v-for="room in roomOptions" :key="room" :value="room">{{ room }}</option>
                </select>
              </div>
            </div>

            <div class="field">
              <label>Your rating</label>
              <div class="star-picker">
                <button
                  v-for="n in 5"
                  :key="n"
                  type="button"
                  class="star-btn"
                  :class="{ filled: n <= (hoverRating || newReview.rating) }"
                  @mouseenter="hoverRating = n"
                  @mouseleave="hoverRating = 0"
                  @click="newReview.rating = n"
                  :aria-label="`${n} star`"
                >
                  <i class="fa-solid fa-star"></i>
                </button>
                <span class="star-picker-value" v-if="newReview.rating">{{ newReview.rating }}/5</span>
              </div>
            </div>

            <div class="field">
              <label>Review title <span class="field-optional">(optional)</span></label>
              <input v-model="newReview.title" type="text" placeholder="Sum up your stay in a few words" />
            </div>

            <div class="field">
              <label>Your review</label>
              <textarea
                v-model="newReview.text"
                rows="4"
                placeholder="Tell other travelers about your experience..."
                required
              ></textarea>
            </div>

            <p v-if="!writeFormValid && writeFormMissing.length" class="write-missing">
              <i class="fa-solid fa-circle-info"></i>
              Still needed: {{ writeFormMissing.join(", ") }}
            </p>

            <div class="write-actions">
              <button type="button" class="btn-secondary" @click="toggleWriteForm">Cancel</button>
              <button type="submit" class="btn-primary" :disabled="!writeFormValid">
                Submit review
              </button>
            </div>
          </form>
        </Transition>

        <!-- REVIEW LIST -->
        <div class="review-list">

          <div
            class="review-item"
            v-for="review in filteredReviews"
            :key="review.id"
          >
            <span v-if="review.isNew" class="new-badge">Just now</span>

            <div class="review-top">
              <div class="avatar">{{ review.initial }}</div>
              <div class="review-info">
                <h4>{{ review.name }}</h4>
                <small v-if="review.flag || review.country">{{ review.flag }} {{ review.country }}</small>
              </div>
            </div>

            <div class="stars">
              <i
                v-for="n in 5"
                :key="n"
                class="fa-solid fa-star"
                :class="{ dim: n > (review.rating || 5) }"
              ></i>
            </div>

            <h3 class="review-title" v-if="review.title">
              "{{ review.title }}"
            </h3>

            <p class="review-date" v-if="review.date">
              {{ review.date }}
            </p>

            <p class="review-description">
              {{ review.text }}
            </p>

            <div class="review-ratings" v-if="review.ratings && review.ratings.length">
              <div
                class="rating-row"
                v-for="rating in review.ratings"
                :key="rating.name"
              >
                <span>{{ rating.name }}</span>
                <strong>{{ rating.score }}</strong>
              </div>
            </div>

            <div class="trip-type" v-if="review.room">
              <strong>Room:</strong>
              {{ review.room }}
            </div>

            <div class="hotel-response" v-if="review.response">
              <p class="response-date">{{ review.response.date }}</p>
              <p>{{ review.response.text }}</p>
              <strong>{{ review.response.staff }}</strong>
              <p>{{ review.response.role }}</p>
            </div>
          </div>

        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, computed, watch } from "vue";

const props = defineProps({
  show: Boolean,
  reviews: Array,
  leftRatings: Array,
  rightRatings: Array,
  roomOptions: { type: Array, default: () => [] },
});

defineEmits(["close"]);

// Local working copy so a newly submitted review can be added without
// mutating the prop array directly.
const localReviews = ref([...(props.reviews || [])].map((r, i) => ({ ...r, id: r.id || `seed-${i}` })));

watch(
  () => props.reviews,
  (fresh) => {
    localReviews.value = [...(fresh || [])].map((r, i) => ({ ...r, id: r.id || `seed-${i}` }));
  }
);

/* ---------------- write a review ---------------- */

const showWriteForm = ref(false);
const hoverRating = ref(0);

const roomFilter = ref("");

const filteredReviews = computed(() => {
  if (!roomFilter.value) return localReviews.value;
  return localReviews.value.filter((r) => r.room === roomFilter.value);
});

const newReview = ref({
  name: "",
  room: "",
  rating: 0,
  title: "",
  text: "",
});

function toggleWriteForm() {
  showWriteForm.value = !showWriteForm.value;
  if (!showWriteForm.value) resetForm();
}

function resetForm() {
  newReview.value = { name: "", room: "", rating: 0, title: "", text: "" };
  hoverRating.value = 0;
}

const writeFormMissing = computed(() => {
  const missing = [];
  if (newReview.value.name.trim().length < 1) missing.push("your name");
  if (newReview.value.rating < 1) missing.push("a star rating");
  if (newReview.value.text.trim().length < 5) missing.push("a review");
  return missing;
});

const writeFormValid = computed(() => writeFormMissing.value.length === 0);

function submitReview() {
  if (!writeFormValid.value) return;

  const initial = newReview.value.name.trim().charAt(0).toUpperCase() || "?";

  localReviews.value.unshift({
    id: `new-${Date.now()}`,
    initial,
    name: newReview.value.name.trim(),
    text: newReview.value.text.trim(),
    title: newReview.value.title.trim(),
    room: newReview.value.room,
    rating: newReview.value.rating,
    date: "Just now",
    isNew: true,
  });

  showWriteForm.value = false;
  resetForm();
}
</script>

<style scoped>
* {
  box-sizing: border-box;
  font-family: "Figtree", sans-serif;
}

.modal-overlay {
  position: fixed;
  inset: 0;
  background: rgba(7, 18, 34, 0.6);
  backdrop-filter: blur(max(4px, 0.2778vw));
  display: flex;
  justify-content: center;
  align-items: center;
  z-index: 9999;
  padding: max(20px, 1.3889vw);
}

.modal {
  width: 90%;
  max-width: max(1100px, 76.4vw);
  height: 85vh;
  background: #fff;
  border-radius: max(18px, 1.25vw);
  display: flex;
  flex-direction: column;
  overflow: hidden;
  box-shadow: 0 max(30px, 2.0833vw) max(90px, 6.25vw) rgba(0, 0, 0, 0.3);
}

/* HEADER */

.modal-header {
  display: flex;
  justify-content: center;
  align-items: center;
  position: relative;
  flex-shrink: 0;
  padding: max(22px, 1.5278vw) max(28px, 1.9444vw) max(16px, 1.1111vw);
  border-bottom: max(1px, 0.0694vw) solid #edf0f4;
}

.modal-header h2 {
  margin: 0;
  font-size: max(18px, 1.25vw);
  font-weight: 600;
  color: #1a51ad;
}

.close-btn {
  position: absolute;
  right: max(22px, 1.5278vw);
  width: max(34px, 2.3611vw);
  height: max(34px, 2.3611vw);
  border-radius: 50%;
  border: max(1px, 0.0694vw) solid #e8edf3;
  background: #fff;
  color: #6b7280;
  display: inline-flex;
  align-items: center;
  justify-content: center;
  cursor: pointer;
  font-size: max(13px, 0.9028vw);
  transition: background 0.15s ease, color 0.15s ease;
}
.close-btn:hover {
  background: #1a51ad;
  color: #fff;
}

/* BODY / SCROLL CONTAINER */

.modal-body {
  padding: 0 max(28px, 1.9444vw) max(28px, 1.9444vw);
  overflow-y: auto;
  flex: 1;
  min-height: 0;
}

/* RATING SUMMARY (scrolls away normally) */

.summary-grid {
  display: grid;
  grid-template-columns: max(180px, 12.5vw) 1fr 1fr;
  gap: max(40px, 2.7778vw);
  align-items: start;
  margin-bottom: max(20px, 1.3889vw);
  padding-top: max(24px, 1.6667vw);
}

.circle {
  width: max(140px, 9.7vw);
  height: max(140px, 9.7vw);
  border-radius: 50%;
  background: #1a51ad;
  display: flex;
  justify-content: center;
  align-items: center;
}
.circle-content {
  text-align: center;
}
.circle-content h1 {
  margin: 0;
  color: #fff;
  font-size: max(38px, 2.6vw);
  font-weight: 700;
}
.circle-content span {
  color: rgba(255, 255, 255, 0.85);
  font-size: max(14px, 0.9722vw);
}

.bars {
  display: flex;
  flex-direction: column;
}
.rating-item {
  margin-bottom: max(18px, 1.25vw);
}
.label {
  display: flex;
  justify-content: space-between;
  margin-bottom: max(8px, 0.5556vw);
  color: #2b2f36;
  font-size: max(13px, 0.9028vw);
  font-weight: 500;
}
.progress {
  height: max(7px, 0.4861vw);
  background: #edf0f4;
  border-radius: max(20px, 1.3889vw);
  overflow: hidden;
}
.fill {
  height: 100%;
  background: #1a51ad;
  border-radius: max(20px, 1.3889vw);
}

/* STICKY TOOLBAR */

.sticky-toolbar {
  position: sticky;
  top: 0;
  z-index: 5;
  background: #fff;
  padding-top: max(20px, 1.3889vw);
  padding-bottom: max(14px, 0.9722vw);
  border-bottom: max(1px, 0.0694vw) solid #edf0f4;
  margin-bottom: max(20px, 1.3889vw);
  box-shadow: 0 max(8px, 0.5556vw) max(12px, 0.8333vw) -8px rgba(28, 55, 89, 0.08);
}

.toolbar-top {
  display: flex;
  align-items: center;
  justify-content: space-between;
  gap: max(14px, 0.9722vw);
  margin-bottom: max(14px, 0.9722vw);
}

.all-reviews-title {
  margin: 0;
  font-size: max(18px, 1.25vw);
  color: #1a1a1a;
  font-weight: 600;
}

.write-review-btn {
  display: inline-flex;
  align-items: center;
  gap: max(8px, 0.5556vw);
  flex-shrink: 0;
  height: max(40px, 2.7778vw);
  padding: 0 max(18px, 1.25vw);
  border: none;
  border-radius: max(10px, 0.6944vw);
  background: #021c44;
  color: #fff;
  font-size: max(13px, 0.9028vw);
  font-weight: 600;
  cursor: pointer;
  transition: background 0.15s ease;
}
.write-review-btn:hover {
  background: #032d6b;
}

.review-filters {
  display: flex;
  justify-content: space-between;
  align-items: center;
  gap: max(14px, 0.9722vw);
  flex-wrap: wrap;
}

.filters {
  display: flex;
  gap: max(10px, 0.6944vw);
  flex-wrap: wrap;
}

.filters select {
  height: max(38px, 2.6389vw);
  padding: 0 max(12px, 0.8333vw);
  border: max(1px, 0.0694vw) solid #dfe3e8;
  border-radius: max(8px, 0.5556vw);
  font-size: max(12.5px, 0.8681vw);
  background: #fafbfc;
  color: #444;
  cursor: pointer;
}

.search-box {
  flex: 1;
  min-width: max(180px, 12.5vw);
  max-width: max(260px, 18.0556vw);
  height: max(38px, 2.6389vw);
  padding: 0 max(14px, 0.9722vw);
  border: max(1px, 0.0694vw) solid #dfe3e8;
  border-radius: max(8px, 0.5556vw);
  font-size: max(12.5px, 0.8681vw);
  background: #fafbfc;
}

.search-box:focus,
.filters select:focus {
  outline: none;
  border-color: #1a51ad;
  background: #fff;
}

/* WRITE A REVIEW FORM */

.write-review-form {
  background: #f8f9fb;
  border: max(1px, 0.0694vw) solid #e8edf3;
  border-radius: max(14px, 0.9722vw);
  padding: max(20px, 1.3889vw) max(22px, 1.5278vw);
  margin-bottom: max(20px, 1.3889vw);
}

.field-row {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: max(16px, 1.1111vw);
  margin-bottom: max(16px, 1.1111vw);
}

.field {
  display: flex;
  flex-direction: column;
  gap: max(6px, 0.4167vw);
  margin-bottom: max(16px, 1.1111vw);
}
.field:last-of-type {
  margin-bottom: 0;
}

.field label {
  font-size: max(12px, 0.8333vw);
  font-weight: 600;
  color: #444;
}
.field-optional {
  font-weight: 400;
  color: #9aa3ad;
}

.field input,
.field select,
.field textarea {
  border: max(1px, 0.0694vw) solid #dfe3e8;
  border-radius: max(9px, 0.625vw);
  padding: max(10px, 0.6944vw) max(13px, 0.9028vw);
  font-size: max(13.5px, 0.9375vw);
  font-family: inherit;
  background: #fff;
  color: #1a1a1a;
  resize: vertical;
}
.field input:focus,
.field select:focus,
.field textarea:focus {
  outline: none;
  border-color: #1a51ad;
}

.star-picker {
  display: flex;
  align-items: center;
  gap: max(4px, 0.2778vw);
}
.star-btn {
  border: none;
  background: none;
  padding: max(2px, 0.1389vw);
  cursor: pointer;
  font-size: max(22px, 1.5278vw);
  color: #dfe3e8;
  transition: color 0.1s ease, transform 0.1s ease;
}
.star-btn:hover {
  transform: scale(1.1);
}
.star-btn.filled {
  color: #f5c347;
}
.star-picker-value {
  margin-left: max(8px, 0.5556vw);
  font-size: max(12.5px, 0.8681vw);
  color: #6b7280;
  font-weight: 600;
}

.write-missing {
  display: flex;
  align-items: center;
  gap: max(8px, 0.5556vw);
  margin: 0 0 max(14px, 0.9722vw);
  padding: max(9px, 0.625vw) max(13px, 0.9028vw);
  background: #fff8ec;
  border: max(1px, 0.0694vw) solid #f4e2b8;
  border-radius: max(9px, 0.625vw);
  font-size: max(12px, 0.8333vw);
  color: #8a6d1a;
}

.write-actions {
  display: flex;
  justify-content: flex-end;
  gap: max(10px, 0.6944vw);
}

.btn-secondary {
  height: max(40px, 2.7778vw);
  padding: 0 max(18px, 1.25vw);
  border: max(1px, 0.0694vw) solid #dfe3e8;
  background: #fff;
  border-radius: max(9px, 0.625vw);
  font-size: max(13px, 0.9028vw);
  font-weight: 600;
  color: #444;
  cursor: pointer;
}
.btn-secondary:hover {
  border-color: #1a51ad;
  color: #1a51ad;
}

.btn-primary {
  height: max(40px, 2.7778vw);
  padding: 0 max(20px, 1.3889vw);
  border: none;
  background: #021c44;
  color: #fff;
  border-radius: max(9px, 0.625vw);
  font-size: max(13px, 0.9028vw);
  font-weight: 600;
  cursor: pointer;
  transition: background 0.15s ease;
}
.btn-primary:hover:not(:disabled) {
  background: #032d6b;
}
.btn-primary:disabled {
  opacity: 0.45;
  cursor: not-allowed;
}

.write-form-enter-active,
.write-form-leave-active {
  transition: opacity 0.2s ease, transform 0.2s ease;
}
.write-form-enter-from,
.write-form-leave-to {
  opacity: 0;
  transform: translateY(-6px);
}

/* REVIEW LIST */

.review-list {
  display: flex;
  flex-direction: column;
  gap: max(16px, 1.1111vw);
}

.review-item {
  position: relative;
  border: max(1px, 0.0694vw) solid #e8edf3;
  border-radius: max(14px, 0.9722vw);
  padding: max(20px, 1.3889vw);
  background: #fff;
  transition: box-shadow 0.2s ease;
}
.review-item:hover {
  box-shadow: 0 max(8px, 0.5556vw) max(24px, 1.6667vw) rgba(28, 55, 89, 0.08);
}

.new-badge {
  position: absolute;
  top: max(18px, 1.25vw);
  right: max(18px, 1.25vw);
  font-size: max(10.5px, 0.7292vw);
  font-weight: 700;
  letter-spacing: max(0.4px, 0.0278vw);
  text-transform: uppercase;
  color: #1b7a3d;
  background: #edf8ee;
  border: max(1px, 0.0694vw) solid #bfe5cb;
  padding: max(3px, 0.2083vw) max(9px, 0.625vw);
  border-radius: max(6px, 0.4167vw);
}

.review-top {
  display: flex;
  gap: max(12px, 0.8333vw);
  align-items: center;
  margin-bottom: max(12px, 0.8333vw);
}

.avatar {
  width: max(38px, 2.6389vw);
  height: max(38px, 2.6389vw);
  border-radius: 50%;
  background: #2a6bb0;
  color: #fff;
  display: flex;
  justify-content: center;
  align-items: center;
  font-weight: 700;
  font-size: max(14px, 0.9722vw);
  flex-shrink: 0;
}

.review-info h4 {
  margin: 0;
  font-size: max(14px, 0.9722vw);
  color: #1a1a1a;
}
.review-info small {
  color: #9aa3ad;
  font-size: max(12px, 0.8333vw);
}

.stars {
  color: #f5c347;
  margin-bottom: max(10px, 0.6944vw);
  font-size: max(12px, 0.8333vw);
  display: flex;
  gap: max(2px, 0.1389vw);
}
.stars i.dim {
  color: #e5e5e5;
}

.review-title {
  margin: 0 0 max(5px, 0.3472vw);
  font-size: max(15px, 1.0417vw);
  color: #1a1a1a;
  font-weight: 600;
}

.review-date {
  margin: 0 0 max(10px, 0.6944vw);
  color: #9aa3ad;
  font-size: max(12px, 0.8333vw);
}

.review-description {
  line-height: 1.75;
  color: #56616e;
  font-size: max(13.5px, 0.9375vw);
  margin-bottom: max(14px, 0.9722vw);
}

.review-ratings {
  display: flex;
  flex-wrap: wrap;
  gap: max(8px, 0.5556vw) max(22px, 1.5278vw);
  margin-bottom: max(12px, 0.8333vw);
}
.rating-row {
  display: flex;
  align-items: center;
  gap: max(6px, 0.4167vw);
  font-size: max(12px, 0.8333vw);
  color: #6b7280;
}
.rating-row strong {
  color: #1a51ad;
}

.trip-type {
  font-size: max(12px, 0.8333vw);
  color: #6b7280;
  margin-bottom: max(12px, 0.8333vw);
}

.hotel-response {
  background: #f8f9fb;
  border-radius: max(10px, 0.6944vw);
  padding: max(14px, 0.9722vw) max(16px, 1.1111vw);
  font-size: max(12px, 0.8333vw);
  color: #56616e;
}
.hotel-response p {
  margin: 0 0 max(6px, 0.4167vw);
}
.hotel-response strong {
  color: #1a1a1a;
}
/* RESPONSIVE */

@media (max-width: 900px) {
  .summary-grid {
    grid-template-columns: 1fr;
  }
  .circle {
    margin: 0 auto;
  }
}

@media (max-width: 640px) {
  .modal {
    height: 92vh;
    border-radius: 16px;
  }
  .modal-body {
    padding: 20px;
  }
  .toolbar-top {
    flex-direction: column;
    align-items: stretch;
  }
  .write-review-btn {
    justify-content: center;
  }
  .review-filters {
    flex-direction: column;
    align-items: stretch;
  }
  .search-box {
    max-width: none;
  }
  .field-row {
    grid-template-columns: 1fr;
  }
}
</style>