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
  padding:max(50px, 3.4722vw) 0;
  background:#f8f9fb;
}

.section-header{
  max-width:max(1200px, 83.3333vw);
  margin:auto;
  padding:0 max(20px, 1.3889vw);
}

.eyebrow {
  color: #034acf;
  font-size: clamp(max(10px, 0.6944vw), 0.8vw, max(12px, 0.8333vw));
  font-weight: 700;
  letter-spacing: max(1px, 0.0694vw);
  margin-bottom: clamp(max(8px, 0.5556vw), 1vw, max(12px, 0.8333vw));
}

.section-header h2{
  font-size:max(34px, 2.3611vw);
  font-weight:500;
  color:#1A51AD;
  margin-bottom: max(80px, 5.5556vw);
  line-height:1.3;
}

.overview-grid{
  display:grid;
  grid-template-columns:2fr 1fr;
  gap:max(25px, 1.7361vw);
  align-items:start;
}

/* LEFT CARD */

.hotel-card{
  background:#fff;
  border:max(1px, 0.0694vw) solid #e8edf3;
  border-radius:max(16px, 1.1111vw);
  padding:max(24px, 1.6667vw);
  transition:.3s;
}

.hotel-card:hover{
  transform:translateY(min(-3px, -0.2083vw));
  box-shadow:0 max(10px, 0.6944vw) max(25px, 1.7361vw) rgba(0,0,0,.08);
}

.hotel-card h3{
  font-size:max(26px, 1.8056vw);
  font-weight:600;
  color:#222;
  margin:max(16px, 1.1111vw) 0;
  line-height:1.4;
}

.location-row{
  display:flex;
  align-items:center;
  flex-wrap:wrap;
  gap:max(8px, 0.5556vw);
  margin-bottom:max(22px, 1.5278vw);
}

.location-icon{
  width:max(18px, 1.25vw);
  height:max(18px, 1.25vw);
  color:#034acf;
}

.location-row span{
  font-size:max(10px, 0.6944vw);
  color:#555;
}

.location-row a{
  display:inline-flex;
  align-items:center;
  border-radius:max(6px, 0.4167vw);
  background:#fff;
  color:#374151;
  text-decoration:none;
  font-size:max(10px, 0.6944vw);
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
  gap:max(12px, 0.8333vw);
  margin-bottom:max(22px, 1.5278vw);
}

.score-box{
  width:max(46px, 3.1944vw);
  height:max(46px, 3.1944vw);
  background:#1f2937;
  color:#fff;
  border-radius:max(8px, 0.5556vw);
  display:flex;
  justify-content:center;
  align-items:center;
  font-size:max(18px, 1.25vw);
  font-weight:700;
}

.rating-info h4{
  font-size:max(18px, 1.25vw);
  color:#222;
  margin-bottom:max(4px, 0.2778vw);
}

.rating-info a{
  text-decoration:none;
  color:#034acf;
  font-size:max(14px, 0.9722vw);
}

.rating-info a:hover{
  text-decoration:underline;
}

.hotel-card p{
  color:#555;
  font-size:max(14px, 0.9722vw);
  line-height:1.8;
}

/* RIGHT CARD */

.map-card{
  background:#fff;
  border:max(1px, 0.0694vw) solid #e8edf3;
  border-radius:max(16px, 1.1111vw);
  padding:max(20px, 1.3889vw);
  transition:.3s;
}

.map-card:hover{
  transform:translateY(min(-3px, -0.2083vw));
  box-shadow:0 max(10px, 0.6944vw) max(25px, 1.7361vw) rgba(0,0,0,.08);
}

.map-card h3{
  font-size:max(22px, 1.5278vw);
  margin-bottom:max(18px, 1.25vw);
  font-weight: 600;
  color:#222;
}

.map-container{
  overflow:hidden;
  border-radius:max(12px, 0.8333vw);
  margin-bottom:max(18px, 1.25vw);
}

.map-container iframe{
  width:100%;
  height:max(220px, 15.2778vw);
  border:0;
}

.address{
  color:#666;
  font-size:max(12px, 0.8333vw);
  line-height:1.6;
  margin-bottom:max(16px, 1.1111vw);
}

.map-card>a{
  display:inline-flex;
  align-items:center;
  justify-content:center;
  padding:max(8px, 0.5556vw) max(16px, 1.1111vw);
  border:max(1px, 0.0694vw) solid #d1d5db;
  border-radius:max(6px, 0.4167vw);
  background:#fff;
  color:#374151;
  text-decoration:none;
  font-size:max(12px, 0.8333vw);
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
  border-top:max(1px, 0.0694vw) solid #ececec;
  margin:max(20px, 1.3889vw) 0;
}

.map-card h4{
  font-size:max(16px, 1.1111vw);
  color:#222;
  margin-bottom:max(12px, 0.8333vw);
}

.place-row{
  display:flex;
  justify-content:space-between;
  align-items:center;
  padding:max(10px, 0.6944vw) 0;
  border-bottom:max(1px, 0.0694vw) solid #f1f1f1;
  font-size:max(14px, 0.9722vw);
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
  margin:max(20px, 1.3889vw) 0;
}

.highlights h4{
  font-size:max(18px, 1.25vw);
  color:#222;
  margin-bottom:max(15px, 1.0417vw);
  font-weight:600;
}

.highlight-grid{
  display:grid;
  grid-template-columns:repeat(5,max-content);
  gap:max(14px, 0.9722vw);
}

.highlight-item{
  display:flex;
  align-items:center;
  gap:max(12px, 0.8333vw);
  padding:max(5px, 0.3472vw);
  border:max(1px, 0.0694vw) solid #e8edf3;
  border-radius:max(10px, 0.6944vw);
  background:#fff;
  transition:.25s;
}

.highlight-item:hover{
  border-color:#1A51AD;
  transform:translateY(min(-2px, -0.1389vw));
}

.highlight-item i{
  font-size:max(15px, 1.0417vw);
  color:#1A51AD;
  width:max(24px, 1.6667vw);
}

.highlight-item span{
  font-size:max(13px, 0.9028vw);
  font-weight:500;
  color:#222;
}

@media (max-width:768px){
  .highlight-grid{
    grid-template-columns:1fr;
  }
}
</style>