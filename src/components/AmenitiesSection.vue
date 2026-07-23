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
  max-width:1240px;
  margin:auto;
  padding-inline: clamp(20px, 4vw, 34px);
}

.eyebrow {
  color: #034acf;
  font-size: clamp(10px, 0.8vw, 12px);
  font-weight: 700;
  letter-spacing: 1px;
  margin-bottom: clamp(8px, 1vw, 12px);
}

.section-header h2 {
  font-size: clamp(24px, 3vw, 34px);
  font-weight: 500;
  color: #1A51AD;
  line-height: 1.3;
  margin-bottom: clamp(20px, 3vw, 30px);
}

.amenities-layout {
  position: relative;
  width: 100%;
  margin-top: clamp(24px, 4vw, 40px);
  min-height: clamp(450px, 55vw, 520px);
}

.amenities-card {
  position: absolute;
  top: clamp(-50px, -4vw, -20px);
  right: -60px;
  width: clamp(320px, 38vw, 480px);
  padding: clamp(24px, 4vw, 55px);
  background: rgba(13,69,139,.98);
  color: white;
  min-height: clamp(420px, 50vw, 520px);
}

.image-section{
    width:100%;
}

.amenities-image {
  width: 100vw;
  height: clamp(240px, 31vw, 470px);
  object-fit: cover;
  display: block;
  margin-left: calc(50% - 50vw);
}

.amenities-card h3{
    font-size: clamp(22px, 3vw, 32px);
    font-weight:500;
    color:white;
    margin-bottom: clamp(18px, 2vw, 25px);

}

.amenities-card p{
    color:rgba(255,255,255,.85);
    line-height:1.8;
    margin-bottom: clamp(20px, 3vw, 35px);
    font-size: clamp(13px, 1vw, 14px);
}

.amenities-grid {
  display: grid;
  grid-template-columns: repeat(2,1fr);
  gap: clamp(16px,2vw,22px) clamp(20px,3vw,35px);
  margin-top: clamp(20px,3vw,30px);
}

.amenity{
    display:flex;
    align-items:center;
    gap:14px;
}

.icon {
  width: clamp(18px,2vw,22px);
  height: clamp(18px,2vw,22px);
}

.amenity span {
  font-size: clamp(12px,1vw,14px);
}

.amenities-btn{
    display:inline-flex;
    align-items:center;
    justify-content:center;
    margin-top: clamp(24px,4vw,40px);
    padding: clamp(8px,1vw,10px) clamp(18px,2vw,24px);
    background:#ffffff;
    color:#000000;
    text-decoration:none;
    border-radius:10px;
    font-weight:600;
    transition:.3s ease;
    font-size: clamp(12px,1vw,14px);
}

.amenities-btn:hover{
    background:#d6d6d6;
    transform:translateY(-2px);
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