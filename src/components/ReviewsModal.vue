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
    max-width:max(1200px, 83.3333vw);
    height:85vh;

    background:#fff;
    border-radius:max(20px, 1.3889vw);
    padding:max(30px, 2.0833vw);
}

.modal-header{
    display:flex;
    justify-content:center;
    align-items:center;
    position:relative;
}

.modal-body{

    padding:max(35px, 2.4306vw);

    overflow-y:auto;

    flex:1;
}

.close-btn{
    position:absolute;
    right:0;

    background:none;
    border:none;

    font-size:max(28px, 1.9444vw);
    cursor:pointer;
}

.summary-grid{
    display:grid;
    grid-template-columns:max(220px, 15.2778vw) 1fr 1fr;
    gap:max(50px, 3.4722vw);
    align-items:start;
}

.circle{
    width:max(220px, 15.2778vw);
    height:max(220px, 15.2778vw);
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
    font-size:max(72px, 5vw);
}

.circle-content span{
    color:white;
    font-size:max(28px, 1.9444vw);
}

.rating-item{
    margin-bottom:max(28px, 1.9444vw);
}

.label{
    display:flex;
    justify-content:space-between;
    margin-bottom:max(10px, 0.6944vw);
}

.progress{
    height:max(8px, 0.5556vw);
    background:#ececec;
    border-radius:max(20px, 1.3889vw);
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

    margin-top:max(50px, 3.4722vw);
    margin-bottom:max(40px, 2.7778vw);

    gap:max(20px, 1.3889vw);
}

.filters{
    display:flex;
    gap:max(16px, 1.1111vw);
}

.filters select{

    width:max(180px, 12.5vw);

    height:max(46px, 3.1944vw);

    padding:0 max(16px, 1.1111vw);

    border:max(1px, 0.0694vw) solid #ddd;

    border-radius:max(8px, 0.5556vw);

    font-size:max(15px, 1.0417vw);

    background:white;

    cursor:pointer;
}

.search-box{

    width:max(320px, 22.2222vw);

    height:max(46px, 3.1944vw);

    padding:0 max(16px, 1.1111vw);

    border:max(1px, 0.0694vw) solid #ddd;

    border-radius:max(8px, 0.5556vw);

    font-size:max(15px, 1.0417vw);
}

.search-box:focus,
.filters select:focus{

    outline:none;

    border-color:#1565c0;
}

.all-reviews-title{

    margin-bottom:max(30px, 2.0833vw);

    font-size:max(32px, 2.2222vw);

    color:#222;
}

.review-list{

    display:flex;

    flex-direction:column;

    gap:max(24px, 1.6667vw);
}

.review-item{

    border:max(1px, 0.0694vw) solid #e5e5e5;

    border-radius:max(16px, 1.1111vw);

    padding:max(28px, 1.9444vw);

    background:#fff;

    transition:.3s;
}

.review-item:hover{

    box-shadow:0 max(8px, 0.5556vw) max(24px, 1.6667vw) rgba(0,0,0,.08);
}

.review-top{

    display:flex;

    gap:max(18px, 1.25vw);

    margin-bottom:max(18px, 1.25vw);
}

.avatar{

    width:max(50px, 3.4722vw);

    height:max(50px, 3.4722vw);

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

    margin-top:max(5px, 0.3472vw);

    color:#666;
}

.review-text{

    line-height:1.8;

    color:#555;
}

.read-more{

    margin-top:max(18px, 1.25vw);

    border:none;

    background:none;

    color:#1565c0;

    font-weight:600;

    cursor:pointer;
}




</style>