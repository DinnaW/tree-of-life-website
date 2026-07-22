<template>
  <div class="amenities-section">

    <div class="section-header">
      <div class="eyebrow">
        02 — AMENITIES
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
  padding:0 34px;
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

.amenities-layout{
    position:relative;
    width:100%;
    margin-top:40px;
    min-height:520px;
}

.amenities-card{
    position:absolute;
    top:-50px;
    right:max(calc((100vw - 1240px)/2 + 34px),34px);
    width:480px;
    background:rgba(13, 69, 139, 0.98);
    color:white;
    padding:55px;
    min-height:520px;
}

.image-section{
    width:100%;
}

.amenities-image{
    width:100vw;
    height:430px;
    object-fit:cover;
    display:block;

    margin-left:calc(50% - 50vw);
}

.amenities-card h3{
    font-size:35px;
    font-weight:500;
    color:white;
    margin-bottom:25px;

}

.amenities-card p{
    color:rgba(255,255,255,.85);
    line-height:1.8;
    margin-bottom:35px;
    font-size: 12px;
}

.amenities-grid{
    display:grid;
    grid-template-columns:repeat(2,1fr);
    gap:22px 35px;
    margin-top:30px;
}

.amenity{
    display:flex;
    align-items:center;
    gap:14px;
}

.icon{
    width:22px;
    height:22px;
    color:white;

}

.amenity span{
    color:white;
    font-size:12px;
}

.amenities-btn{
    display:inline-flex;
    align-items:center;
    justify-content:center;
    margin-top:40px;
    padding:8px 20px;
    background:#ffffff;
    color:#000000;
    text-decoration:none;
    border-radius:10px;
    font-weight:600;
    transition:.3s ease;
    font-size: 12px;
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
</style>