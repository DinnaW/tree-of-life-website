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
        >
          ✕
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
        <div class="review-filters">

  <div class="filters">

    <select>
      <option>Date Range</option>
    </select>

    <select>
      <option>Guest Rating</option>
    </select>

    <select>
      <option>Trip Type</option>
    </select>

  </div>

  <input
    type="text"
    placeholder="Search reviews..."
    class="search-box"
  />

</div>
    <h3 class="all-reviews-title">
    All Reviews ({{ reviews.length }})
</h3>

<div class="review-list">

  <div
    class="review-item"
    v-for="review in reviews"
    :key="review.title"
  >

    <div class="stars">
      ★★★★★
    </div>

    <h3 class="review-title">
      "{{ review.title }}"
    </h3>

    <p class="review-date">
      {{ review.date }}
    </p>

    <p class="review-description">
      {{ review.text }}
    </p>

    <div class="review-ratings">

      <div
        class="rating-row"
        v-for="rating in review.ratings"
        :key="rating.name"
      >
        <span>{{ rating.name }}</span>

        <strong>{{ rating.score }}</strong>

      </div>

    </div>

    <div class="trip-type">

      <strong>Trip Type:</strong>

      {{ review.tripType }}

    </div>

    <div class="helpful">

      Was this helpful?

      <button>Yes (0)</button>

      <button>No (0)</button>

    </div>

    <div class="hotel-response">

      <button class="response-toggle">
        Hide hotel response
      </button>

      <p class="response-date">
        {{ review.response.date }}
      </p>

      <p>
        {{ review.response.text }}
      </p>

      <strong>
        {{ review.response.staff }}
      </strong>

      <p>
        {{ review.response.role }}
      </p>

    </div>

  </div>

</div>
    </div>
    </div>
  </div>
</template>

<script setup>
defineProps({
  show: Boolean,
  reviews: Array,
  leftRatings: Array,
  rightRatings: Array,
})

defineEmits(["close"])

</script>

<style scoped>
.modal-overlay{
    position:fixed;
    inset:0;
    background:rgba(0,0,0,.55);

    display:flex;
    justify-content:center;
    align-items:center;

    z-index:9999;
}

.modal{
    width:90%;
    max-width:1200px;
    height:85vh;

    background:#fff;
    border-radius:20px;
    padding:30px;
}

.modal-header{
    display:flex;
    justify-content:center;
    align-items:center;
    position:relative;
}

.modal-body{

    padding:35px;

    overflow-y:auto;

    flex:1;
}

.close-btn{
    position:absolute;
    right:0;

    background:none;
    border:none;

    font-size:28px;
    cursor:pointer;
}

.summary-grid{
    display:grid;
    grid-template-columns:220px 1fr 1fr;
    gap:50px;
    align-items:start;
}

.circle{
    width:220px;
    height:220px;
    border-radius:50%;
    background:#8eb8df;

    display:flex;
    justify-content:center;
    align-items:center;
}

.circle-content{
    text-align:center;
}

.circle-content h1{
    margin:0;
    color:white;
    font-size:72px;
}

.circle-content span{
    color:white;
    font-size:28px;
}

.rating-item{
    margin-bottom:28px;
}

.label{
    display:flex;
    justify-content:space-between;
    margin-bottom:10px;
}

.progress{
    height:8px;
    background:#ececec;
    border-radius:20px;
    overflow:hidden;
}

.fill{
    height:100%;
    background:#7db5ea;
}

.review-filters{
    display:flex;
    justify-content:space-between;
    align-items:center;

    margin-top:50px;
    margin-bottom:40px;

    gap:20px;
}

.filters{
    display:flex;
    gap:16px;
}

.filters select{

    width:180px;

    height:46px;

    padding:0 16px;

    border:1px solid #ddd;

    border-radius:8px;

    font-size:15px;

    background:white;

    cursor:pointer;
}

.search-box{

    width:320px;

    height:46px;

    padding:0 16px;

    border:1px solid #ddd;

    border-radius:8px;

    font-size:15px;
}

.search-box:focus,
.filters select:focus{

    outline:none;

    border-color:#1565c0;
}

.all-reviews-title{

    margin-bottom:30px;

    font-size:32px;

    color:#222;
}

.review-list{

    display:flex;

    flex-direction:column;

    gap:24px;
}

.review-item{

    border:1px solid #e5e5e5;

    border-radius:16px;

    padding:28px;

    background:#fff;

    transition:.3s;
}

.review-item:hover{

    box-shadow:0 8px 24px rgba(0,0,0,.08);
}

.review-top{

    display:flex;

    gap:18px;

    margin-bottom:18px;
}

.avatar{

    width:50px;

    height:50px;

    border-radius:50%;

    background:#1565c0;

    color:white;

    display:flex;

    justify-content:center;

    align-items:center;

    font-weight:700;
}

.review-info h4{

    margin:0;
}

.review-info small{

    color:#888;
}

.review-info p{

    margin-top:5px;

    color:#666;
}

.review-text{

    line-height:1.8;

    color:#555;
}

.read-more{

    margin-top:18px;

    border:none;

    background:none;

    color:#1565c0;

    font-weight:600;

    cursor:pointer;
}




</style>