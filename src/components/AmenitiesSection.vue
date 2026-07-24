<template>
  <div class="amenities-section">

    <div class="section-header">
      <div class="eyebrow">
         — AMENITIES
      </div>

      <h2>
        Everything You Need to Relax
      </h2>

      <div class="amenities-layout">

          <!-- Image -->
          <div class="image-section">
            <img
              :src="`${baseUrl}images/amenities.png`"
              alt="Amenities"
              class="amenities-image"
            />
          </div>

          <!-- Floating Card -->
          <div class="amenities-card">

            <h3>Our Amenities</h3>

            <p>
              Experience thoughtfully designed facilities surrounded by nature.
              From luxury accommodation and wellness experiences to fine dining
              and recreation, everything is created for a memorable stay.
            </p>

            <div class="amenities-grid">

              <div
                class="amenity"
                v-for="item in amenities"
                :key="item.text"
              >
                <component :is="item.icon" class="icon"/>

                <span>{{ item.text }}</span>

              </div>

            </div>

            <a href="#" class="amenities-btn">
              Explore Resort →
            </a>

          </div>

        </div>

    </div>
  </div>    
</template>

<script setup>
import {
  Trees,
  HeartPulse,
  BedDouble,
  Waves,
  House,
  Trophy,
  UtensilsCrossed,
  Baby
} from "lucide-vue-next";

const baseUrl = import.meta.env.BASE_URL;

const amenities = [
  {
    icon: Trees,
    text: "65 Acres Resort"
  },
  {
    icon: HeartPulse,
    text: "Ayurveda Centre"
  },
  {
    icon: BedDouble,
    text: "43 Luxury Rooms"
  },
  {
    icon: Waves,
    text: "Infinity Pool"
  },
  {
    icon: House,
    text: "4 Private Villas"
  },
  {
    icon: Trophy,
    text: "Billiard Room"
  },
  {
    icon: UtensilsCrossed,
    text: "Restaurant & Bar"
  },
  {
    icon: Baby,
    text: "Children's Park"
  }
];
</script>

<style scoped>

*{
  margin:0;
  padding:0;
  box-sizing:border-box;
  font-family: "Figtree", sans-serif;
}

.amenities-section{
    width:100%;
}

.section-header{
  max-width:max(1240px, 86.1111vw);
  margin:auto;
  padding-inline: clamp(max(20px, 1.3889vw), 4vw, max(34px, 2.3611vw));
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

.amenities-layout {
  position: relative;
  width: 100%;
  margin-top: clamp(max(24px, 1.6667vw), 4vw, max(40px, 2.7778vw));
  min-height: clamp(max(450px, 31.25vw), 55vw, max(520px, 36.1111vw));
}

.amenities-card {
  position: absolute;
  top: clamp(min(-50px, -3.4722vw), -4vw, min(-20px, -1.3889vw));
  right: min(-60px, -4.1667vw);
  width: clamp(max(320px, 22.2222vw), 38vw, max(480px, 33.3333vw));
  padding: clamp(max(24px, 1.6667vw), 4vw, max(55px, 3.8194vw));
  background: rgba(13,69,139,.98);
  color: white;
  min-height: clamp(max(420px, 29.1667vw), 50vw, max(520px, 36.1111vw));
}

.image-section{
    width:100%;
}

.amenities-image {
  width: 100vw;
  height: clamp(max(240px, 16.6667vw), 31vw, max(470px, 32.6389vw));
  object-fit: cover;
  display: block;
  margin-left: calc(50% - 50vw);
}

.amenities-card h3{
    font-size: clamp(max(22px, 1.5278vw), 3vw, max(32px, 2.2222vw));
    font-weight:500;
    color:white;
    margin-bottom: clamp(max(18px, 1.25vw), 2vw, max(25px, 1.7361vw));

}

.amenities-card p{
    color:rgba(255,255,255,.85);
    line-height:1.8;
    margin-bottom: clamp(max(20px, 1.3889vw), 3vw, max(35px, 2.4306vw));
    font-size: clamp(max(13px, 0.9028vw), 1vw, max(14px, 0.9722vw));
}

.amenities-grid {
  display: grid;
  grid-template-columns: repeat(2,1fr);
  gap: clamp(max(16px, 1.1111vw),2vw,max(22px, 1.5278vw)) clamp(max(20px, 1.3889vw),3vw,max(35px, 2.4306vw));
  margin-top: clamp(max(20px, 1.3889vw),3vw,max(30px, 2.0833vw));
}

.amenity{
    display:flex;
    align-items:center;
    gap:max(14px, 0.9722vw);
}

.icon {
  width: clamp(max(18px, 1.25vw),2vw,max(22px, 1.5278vw));
  height: clamp(max(18px, 1.25vw),2vw,max(22px, 1.5278vw));
}

.amenity span {
  font-size: clamp(max(12px, 0.8333vw),1vw,max(14px, 0.9722vw));
}

.amenities-btn{
    display:inline-flex;
    align-items:center;
    justify-content:center;
    margin-top: clamp(max(24px, 1.6667vw),4vw,max(40px, 2.7778vw));
    padding: clamp(max(8px, 0.5556vw),1vw,max(10px, 0.6944vw)) clamp(max(18px, 1.25vw),2vw,max(24px, 1.6667vw));
    background:#ffffff;
    color:#000000;
    text-decoration:none;
    border-radius:max(10px, 0.6944vw);
    font-weight:600;
    transition:.3s ease;
    font-size: clamp(max(12px, 0.8333vw),1vw,max(14px, 0.9722vw));
}

.amenities-btn:hover{
    background:#d6d6d6;
    transform:translateY(min(-2px, -0.1389vw));
}

.amenities-full{
    padding:0;
    border:none;
    min-height:auto;
}

@media (max-width:900px){

.amenities-layout{
    min-height:auto;
}

.amenities-card{
    position:relative;
    top:auto;
    right:auto;
    width:100%;
    margin-top:-40px;
    padding:35px;
}

.amenities-grid{
    grid-template-columns:1fr;
}

@media (max-width:1240px){
  .amenities-card{
    width:400px;
    padding:40px;
  }
 
  .amenities-card h3{
    font-size:28px;
  }
}
 
@media (max-width:1024px){
  .amenities-card{
    width:340px;
    padding:32px;
    right:20px;
  }
 
  .amenities-card h3{
    font-size:24px;
    margin-bottom:18px;
  }
 
  .amenities-card p{
    margin-bottom:24px;
  }
 
  .amenities-grid{
    gap:16px 20px;
    margin-top:20px;
  }
 
}
 
/* MOBILE — fully stacked layout */
@media (max-width:900px){
 
.amenities-layout{
    min-height:auto;
 
}
 
.image-section{
    width:100%;
 
}
 
.amenities-image{
    height:320px;
 
}
 
.amenities-card{
    position:relative;
    top:auto;
    right:auto;
    width:100%;
    margin-top:-40px;
    padding:35px;
 
}
 
.amenities-grid{
    grid-template-columns:1fr;
 
}
 
}
 
/* SMALL PHONES — tighten padding, type and image height further */
@media (max-width:480px){
 
  .section-header{
    padding:0 20px;
  }

  .amenities-image{
    height:240px;
  }
 
  .amenities-card{
    padding:24px;
    margin-top:-30px;
  }
 
  .amenities-card h3{
    font-size:22px;
  }
 
  .amenities-card p{
    font-size:13px;
    margin-bottom:20px;
  }
 
  .amenities-btn{
    width:100%;
  }
}
}
</style>