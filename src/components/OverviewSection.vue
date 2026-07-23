<template>
  <div class="overview-section">

    <div class="section-header">
      <div class="eyebrow">
         — OVERVIEW
      </div>

      <h2>
        Experience charm and elegance
      </h2>

      <div class="overview-grid">

            <div class="left-column">
                <div class="hotel-card">

                    <h3>Tree of Life Nature Resort</h3>

                        <div class="location-row">
                             <svg class="location-icon" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
                                <path d="M12 21s7-6 7-11a7 7 0 1 0-14 0c0 5 7 11 7 11z"/>
                                <circle cx="12" cy="10" r="2.5"/>
                            </svg>

                            <span>
                                Experience Luxury in the Heart of Nature, 20000 Kandy, Sri Lanka -
                            </span>

                            <a href="https://maps.app.goo.gl/RMRnaL3vBiwGT4NH9" bl>Great location - show map</a>
                        </div>

                        <div class="rating-row">

                            <div class="score-box">
                                9.8
                            </div>

                            <div class="rating-info">
                                <h4>Exceptional</h4>
                                <a href="#">Based on 324 verified reviews ›</a>
                            </div>

                        </div>

                        <p>
                            As green-leafed umbrellas shelter the rich, brown soil, the birds melodiously sing along, their greetings of innocence 
                            coming from the path of rich, brown soil that leads to our humble dwellings. As one of the best holiday nature hotels in 
                            Kandy, “Tree of Life“ makes an everlasting bond between you and nature. Hailed as one of the most popular among Kandy hotels, 
                            Tree of Life is different from other Kandy Hotels due to the presence of unique features that offer you the best accommodation 
                            in Kandy, as well as hotel facilities & Ayurveda treatments that make your holidays more enjoyable with pure happiness. 
                            <br> <br>
                            A nature resort with 65 acres of peaceful, calm area. The hotel provides accommodation and Ayurvedic treatment. 43 Luxurious 
                            rooms, including 04 villas, equipped with mini bar, Tea/Coffee maker, Satellite television, bath with hot/cold water, hair dryer, 
                            and private balcony with panoramic view.


                            <br> <br>
                            
                        </p>

                
                <div class="highlights">
                  <h4>Property Highlights</h4>

                  <div class="highlight-grid">
                    <div
                      v-for="item in highlights"
                      :key="item.name"
                      class="highlight-item"
                    >
                      <i :class="item.icon"></i>
                      <span>{{ item.name }}</span>
                    </div>
                  </div>
                </div>        
            </div>
            </div>
            <div class="right-column">
                <div class="map-card">
                    <h3>Explore the area</h3>

                    <div class="map-container">
                        <iframe src="https://www.google.com/maps/embed?pb=!1m18!1m12!1m3!1d1978.648808227874!2d80.5780325547699!3d7.320427978704727!2m3!1f0!2f0!3f0!3m2!1i1024!2i768!4f13.1!3m3!1m2!1s0x3ae369c7c4f3e63b%3A0xf48bd32fdfed9a0c!2sHotel%20Tree%20of%20Life%20Nature%20Resort!5e0!3m2!1sen!2slk!4v1784477083460!5m2!1sen!2slk" 
                            style="border:0;" 
                            allowfullscreen="" 
                            loading="lazy" 
                            referrerpolicy="strict-origin-when-cross-origin">
                        </iframe>

                    </div>

                    <p class="address">
                        607,
                        10 Colombo Road,
                        Kandy,
                        Sri Lanka
                    </p>

                    <a href="https://maps.app.goo.gl/Fm3uyD331J7DZWAr5" target="blank"> View on map → </a>
               
                    <hr>

                    <h4> Places nearby </h4>
                    
                    <div
                        v-for="place in nearbyPlaces"
                        :key="place.name"
                        class="place-row"
                    >

                        <span>{{ place.name }}</span>
                        <span>{{ place.time }}</span>
                    </div>
                </div>    
            </div>
        </div>
    </div>

  </div>
</template>

<script setup>
const nearbyPlaces = [

{
name:"Pitiwella Beach",
time:"16 min walk"
},

{
name:"Galle Fort",
time:"7 min drive"
},

{
name:"Unawatuna Beach",
time:"13 min drive"
}

]

const highlights = [
  { icon: "fa-solid fa-tree", name: "Nature Resort" },
  { icon: "fa-solid fa-water-ladder", name: "Infinity Pool" },
  { icon: "fa-solid fa-utensils", name: "Restaurant" },
  { icon: "fa-solid fa-square-parking", name: "Free Parking" },
  { icon: "fa-solid fa-wifi", name: "Free WiFi" },
  
];
</script>

<style scoped>

*{
  margin:0;
  padding:0;
  box-sizing:border-box;
  font-family: 'Figtree';
}

.overview-section{
  width:100%;
  padding:50px 0;
  background:#f8f9fb;
}

.section-header{
  max-width:1200px;
  margin:auto;
  padding:0 20px;
}

.eyebrow{
    color:#034acf;
    font-size:10px;
    font-weight:700;
    letter-spacing:1px;
    margin-bottom:10px;
}

.section-header h2{
  font-size:34px;
  font-weight:500;
  color:#1A51AD;
  margin-bottom:30px;
  line-height:1.3;
}

.overview-grid{
  display:grid;
  grid-template-columns:2fr 1fr;
  gap:25px;
  align-items:start;
}

/* LEFT CARD */

.hotel-card{
  background:#fff;
  border:1px solid #e8edf3;
  border-radius:16px;
  padding:24px;
  transition:.3s;
}

.hotel-card:hover{
  transform:translateY(-3px);
  box-shadow:0 10px 25px rgba(0,0,0,.08);
}

.hotel-card h3{
  font-size:26px;
  font-weight:600;
  color:#222;
  margin:16px 0;
  line-height:1.4;
}

.location-row{
  display:flex;
  align-items:center;
  flex-wrap:wrap;
  gap:8px;
  margin-bottom:22px;
}

.location-icon{
  width:18px;
  height:18px;
  color:#034acf;
}

.location-row span{
  font-size:10px;
  color:#555;
}

.location-row a{
  display:inline-flex;
  align-items:center;
  border-radius:6px;
  background:#fff;
  color:#374151;
  text-decoration:none;
  font-size:10px;
  font-weight:650;
  transition:.3s;
}

.location-row a:hover{
  background:#f8fafc;
  border-color:#034acf;
  color:#034acf;
}

.location-row a:hover{
  text-decoration:underline;
}

/* RATING */

.rating-row{
  display:flex;
  align-items:center;
  gap:12px;
  margin-bottom:22px;
}

.score-box{
  width:46px;
  height:46px;
  background:#1f2937;
  color:#fff;
  border-radius:8px;
  display:flex;
  justify-content:center;
  align-items:center;
  font-size:18px;
  font-weight:700;
}

.rating-info h4{
  font-size:18px;
  color:#222;
  margin-bottom:4px;
}

.rating-info a{
  text-decoration:none;
  color:#034acf;
  font-size:14px;
}

.rating-info a:hover{
  text-decoration:underline;
}

.hotel-card p{
  color:#555;
  font-size:14px;
  line-height:1.8;
}

/* RIGHT CARD */

.map-card{
  background:#fff;
  border:1px solid #e8edf3;
  border-radius:16px;
  padding:20px;
  transition:.3s;
}

.map-card:hover{
  transform:translateY(-3px);
  box-shadow:0 10px 25px rgba(0,0,0,.08);
}

.map-card h3{
  font-size:22px;
  margin-bottom:18px;
  font-weight: 600;
  color:#222;
}

.map-container{
  overflow:hidden;
  border-radius:12px;
  margin-bottom:18px;
}

.map-container iframe{
  width:100%;
  height:220px;
  border:0;
}

.address{
  color:#666;
  font-size:12px;
  line-height:1.6;
  margin-bottom:16px;
}

.map-card>a{
  display:inline-flex;
  align-items:center;
  justify-content:center;
  padding:8px 16px;
  border:1px solid #d1d5db;
  border-radius:6px;
  background:#fff;
  color:#374151;
  text-decoration:none;
  font-size:12px;
  font-weight:500;
  transition:.3s;
}

.map-card>a:hover{
  background:#f8fafc;
  border-color:#034acf;
  color:#034acf;
}

.map-card>a:hover{
  text-decoration:underline;
}

.map-card hr{
  border:none;
  border-top:1px solid #ececec;
  margin:20px 0;
}

.map-card h4{
  font-size:16px;
  color:#222;
  margin-bottom:12px;
}

.place-row{
  display:flex;
  justify-content:space-between;
  align-items:center;
  padding:10px 0;
  border-bottom:1px solid #f1f1f1;
  font-size:14px;
}

.place-row:last-child{
  border-bottom:none;
}

.place-row span:first-child{
  color:#444;
}

.place-row span:last-child{
  color:#034acf;
  font-weight:600;
}

/* Tablet */

@media(max-width:992px){

  .overview-grid{
    grid-template-columns:1fr;
  }

  .section-header h2{
    font-size:30px;
  }

  .hotel-card,
  .map-card{
    padding:20px;
  }

}

/* Mobile */

@media(max-width:768px){

  .overview-section{
    padding:40px 0;
  }

  .section-header{
    padding:0 15px;
  }

  .section-header h2{
    font-size:26px;
  }

  .hotel-card h3{
    font-size:24px;
  }

  .rating-row{
    align-items:flex-start;
  }

  .score-box{
    width:42px;
    height:42px;
    font-size:18px;
  }

  .map-container iframe{
    height:200px;
  }

}

.highlights{
  margin:20px 0;
}

.highlights h4{
  font-size:18px;
  color:#222;
  margin-bottom:15px;
  font-weight:600;
}

.highlight-grid{
  display:grid;
  grid-template-columns:repeat(5,max-content);
  gap:14px;
}

.highlight-item{
  display:flex;
  align-items:center;
  gap:12px;
  padding:5px;
  border:1px solid #e8edf3;
  border-radius:10px;
  background:#fff;
  transition:.25s;
}

.highlight-item:hover{
  border-color:#1A51AD;
  transform:translateY(-2px);
}

.highlight-item i{
  font-size:15px;
  color:#1A51AD;
  width:24px;
}

.highlight-item span{
  font-size:13px;
  font-weight:500;
  color:#222;
}

@media (max-width:768px){
  .highlight-grid{
    grid-template-columns:1fr;
  }
}
</style>