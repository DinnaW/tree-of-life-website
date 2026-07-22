<template>
  <section class="rooms-section">
    <div class="section-header">
      <div class="eyebrow">03 — ROOMS</div>
      <h2>Choose your room</h2>

    

    <div class="bar-wrap">
      <AvailabilityBar
        v-model:start-date="startDate"
        v-model:end-date="endDate"
        v-model:travelers="travelers"
        v-model:room-count="roomCount"
        v-model:filter="filter"
        :total="rooms.length"
        :shown="filteredRooms.length"
      />
    </div>

    <p class="subhead">
        {{ rooms.length }} room types · flexible bed and board plans, priced per room
      </p>
    </div>

    <article class="room-card" v-for="room in rooms" :key="room.id">
      <!-- IMAGE -->
      <div class="room-image-box">
        <img :src="room.image" :alt="room.name" class="room-image" />
        <span class="room-tag">{{ room.tag }}</span>
      </div>

      <!-- DETAILS -->
      <div class="room-details">
        <div class="room-title">
          <h3>{{ room.name }}</h3>

          <span v-if="room.mostBooked" class="badge">
            MOST BOOKED
          </span>
        </div>
        <p class="room-meta">{{ room.meta }}</p>

        <div class="options-row">
          <div class="option">
            <label>Bed plan</label>

            <div class="pill-group" role="group" aria-label="Bed plan">
              <button
                v-for="bed in ['Single', 'Double', 'Triple']"
                :key="bed"
                type="button"
                class="pill"
                :class="{ active: room.bed === bed }"
                @click="room.bed = bed"
              >
                {{ bed }}
              </button>
            </div>
          </div>

          <div class="option">
            <label>Meal plan</label>

            <div class="pill-group" role="group" aria-label="Meal plan">
              <button
                v-for="meal in ['Bed & Breakfast', 'Half Board']"
                :key="meal"
                type="button"
                class="pill"
                :class="{ active: room.meal === meal }"
                @click="room.meal = meal"
              >
                {{ meal }}
              </button>
            </div>
          </div>
        </div>

        <div class="facilities-card">

          <div class="facility-grid">
            <div
              class="facility-item"
              v-for="facility in room.facilities"
              :key="facility.name"
            >
              <i :class="facility.icon"></i>
              <span>{{ facility.name }}</span>
            </div>
          </div>
        </div>
      </div>

      <!-- TICKET DIVIDER (signature element) -->
      <div class="divider" aria-hidden="true">
      </div>

      <!-- BOOKING PANEL -->
      <div class="room-options">
        <div class="counter-row">
          <span>Children</span>
          <div class="counter">
            <button
              type="button"
              aria-label="Decrease children"
              :disabled="room.children === 0"
              @click="decrement(room, 'children')"
            >−</button>
            <span class="counter-value">{{ room.children }}</span>
            <button type="button" aria-label="Increase children" @click="room.children++">+</button>
          </div>
        </div>

        <div class="counter-row">
          <span>Extra beds</span>
          <div class="counter">
            <button
              type="button"
              aria-label="Decrease extra beds"
              :disabled="room.extraBeds === 0"
              @click="decrement(room, 'extraBeds')"
            >−</button>
            <span class="counter-value">{{ room.extraBeds }}</span>
            <button type="button" aria-label="Increase extra beds" @click="room.extraBeds++">+</button>
          </div>
        </div>

        <div class="counter-row">
          <span>Rooms</span>
          <div class="select-wrap">
            <select v-model="room.roomCount" aria-label="Number of rooms">
              <option v-for="n in 5" :key="n" :value="n">{{ n }} {{ n > 1 ? 'rooms' : 'room' }}</option>
            </select>
          </div>
        </div>

        <div class="price-block">
          <p v-if="room.roomsLeft" class="rooms-left">
            Only {{ room.roomsLeft }} room left
          </p>

          <div v-if="room.offer" class="offer-badge">
            <i class="fa-solid fa-tags"></i>
            {{ room.offer }}
          </div>

          <div class="price-row">
            <span class="price">${{ total(room) }}</span>
            <span class="price-caption">total · taxes & fees included</span>
          </div>

          <button class="reserve-btn">Reserve this room</button>
        </div>
      </div>
    </article>
  </section>
</template>

<script setup>
import { ref, computed } from "vue";
import AvailabilityBar from "./AvailabilityBar.vue";

const baseUrl = import.meta.env.BASE_URL;

const startDate = ref("");
const endDate = ref("");
const travelers = ref(2);
const roomCount = ref(1);
const filter = ref("all");

const filteredRooms = computed(() => {
  return rooms.value;
});

const rooms = ref([
  {
    id: 1,
    mostBooked: true,
    name: "Panoramic Deluxe",
    image: `${baseUrl}images/room1.jpg`,
    tag: "Panoramic view",
    meta: "38 m² · King bed · Private balcony",
    price: 78,

    bed: "Single",
    meal: "Bed & Breakfast",

    children: 0,
    extraBeds: 0,
    roomCount: 1,

    facilities: [
      { icon: "fa-solid fa-wifi", name: "Free WiFi" },
      { icon: "fa-solid fa-tv", name: "Smart TV" },
      { icon: "fa-solid fa-snowflake", name: "Air Conditioning" },
      { icon: "fa-solid fa-mug-hot", name: "Tea/Coffee" },
      { icon: "fa-solid fa-tree", name: "Garden View" }
    
    ]
  },
  {
    id: 2,
    mostBooked: false,
    name: "Green Zone Deluxe",
    image: `${baseUrl}images/room2.jpg`,
    tag: "Garden view",
    meta: "34 m² · Canopy bed · Private terrace",
    price: 105,
    roomsLeft: 1,
    bed: "Single",
    meal: "Bed & Breakfast",
    offer: "Save 20% Today",

    children: 0,
    extraBeds: 0,
    roomCount: 1,

    facilities: [
      { icon: "fa-solid fa-wifi", name: "Free WiFi" },
      { icon: "fa-solid fa-tv", name: "Smart TV" },
      { icon: "fa-solid fa-snowflake", name: "Air Conditioning" },
      { icon: "fa-solid fa-temperature-half", name: "Hot Water" },
      { icon: "fa-solid fa-bell-concierge", name: "Room Service" },
      { icon: "fa-solid fa-sun", name: "Balcony" },
      { icon: "fa-solid fa-wine-glass", name: "Mini Bar" },
      { icon: "fa-solid fa-tree", name: "Garden View" },
      { icon: "fa-solid fa-paw", name: "Pet Friendly" },
      { icon: "fa-solid fa-smoking", name: "Smoking Area" },
      
    ]
  }
]);

function decrement(room, key) {
  room[key] = Math.max(0, room[key] - 1);
}

const EXTRA_BED_FEE = 15;

function total(room) {
  return room.price * room.roomCount + room.extraBeds * EXTRA_BED_FEE;
}


</script>

<style scoped>

*{
  margin:0;
  padding:0;
  box-sizing:border-box;
  font-family: "Figtree", sans-serif;
}

.rooms-section {
  --color-bg: #faf7f1;
  --color-surface: #ffffff;
  --color-ink: #050505;
  --color-ink-soft: #5d5d5d;
  --color-line: #787c79;
  --color-pine: #073C94;
  --color-pine-soft: #eef1ec;
  --color-gold: #2ed4e3;
  --color-gold-deep: #413f3f;

  max-width: 1340px;
  padding: 50px 50px;
  background:  #f8f9fb;
  color: var(--color-ink);
}

/* SECTION HEAD */

.section-head {
  margin-bottom: 48px;
}

.eyebrow {
  color:#034acf;
  font-size: 10px;
  font-weight: 700;
  letter-spacing: 1px;
  margin-bottom:10px;
}

.rooms-section h2 {
  font-size:34px;
  font-weight:500;
  color:#1A51AD;
  margin-bottom:30px;
  line-height:1.3;
}

.subhead {
  margin-bottom: 20px;
  font-size: 12px;
  color: var(--color-ink-soft);
}

/* ROOM CARD */

.room-card {
  position: relative;
  display: grid;
  grid-template-columns: 300px 1fr 24px 260px;
  align-items: stretch;
  gap: 0;
  background: var(--color-surface);
  border-radius: 20px;
  margin-bottom: 28px;
  box-shadow: 0 1px 2px rgba(31, 46, 36, 0.04);
  transition: box-shadow 0.25s ease, transform 0.25s ease;
  
}

.room-card:hover {
  box-shadow: 0 18px 40px rgba(31, 46, 36, 0.1);
  transform: translateY(-2px);
}

/* IMAGE */

.room-image-box {
  position: relative;
  width: 100%;
  height: 100%;
  min-height: 280px;
  overflow: hidden;
  border-radius: 20px 0px 0px 20px;
}

.room-image {
  width: 100%;
  height: 100%;
  object-fit: cover;
  display: block;
}

.room-tag {
  position: absolute;
  left: 14px;
  bottom: 14px;
  padding: 6px 14px;
  border-radius: 20px;
  background: rgba(68, 68, 68, 0.55);
  backdrop-filter: blur(6px);
  color: #fff;
  font-size: 12px;
  font-weight: 500;
  letter-spacing: 0.3px;
}

/* ROOM DETAILS */
.room-title {
  display: flex;
  align-items: center;
  gap: 12px;
  margin-bottom: 6px;
}

.room-title h3 {
  margin: 0;
  font-size: 26px;
  font-weight: 500;
  color: var(--color-pine);
}

.badge {
  display: inline-flex;
  align-items: center;
  justify-content: center;
  height: 28px;
  padding: 0 16px;
  background: #eef3fa;
  color: #1a51ad;
  font-size: 11px;
  font-weight: 600;
  letter-spacing: 1px;
  text-transform: uppercase;
  border-radius: 4px;
}

.room-details {
  padding: 32px 32px 32px 30px;
}

.room-details h3 {
  margin: 0 0 6px;
 
  font-size: 26px;
  font-weight: 500;
  color: var(--color-pine);
}

.room-meta {
  margin: 0 0 26px;
  font-size: 13.5px;
  color: var(--color-ink-soft);
}

.option {
  margin-bottom: 20px;
}

.option:last-child {
  margin-bottom: 0;
}

.option label {
  display: block;
  font-size: 12.5px;
  color: var(--color-ink-soft);
  font-weight: 600;
  letter-spacing: 0.4px;
  text-transform: uppercase;
  margin-bottom: 10px;
}

.rooms-left {
  margin-bottom: 10px;
  color: #d32f2f;
  font-size: 13px;
  font-weight: 600;
}

/* PILL BUTTONS */

.pill-group {
  display: flex;
  gap: 8px;
  flex-wrap: wrap;
}

.pill {
  height: 38px;
  padding: 0 18px;
  border-radius: 10px;
  border: 1px solid var(--color-line);
  background: #fff;
  color: var(--color-ink);
 
  font-size: 12px;
  font-weight: 500;
  cursor: pointer;
  transition: background 0.15s ease, border-color 0.15s ease, color 0.15s ease;
}

.pill:hover {
  border-color: var(--color-pine);
}

.pill.active {
  background: var(--color-pine);
  border-color: var(--color-pine);
  color: #fff;
}

/* TICKET DIVIDER (signature) */

.divider {
  position: relative;
  border-left: 1px dashed var(--color-line);
  margin: 24px 0;
}

.notch {
  position: absolute;
  left: -9px;
  width: 18px;
  height: 18px;
  border-radius: 50%;
  background: var(--color-bg);
}

.notch-top {
  top: -24px;
}

.notch-bottom {
  bottom: -24px;
}

/* BOOKING PANEL */

.room-options {
  padding: 28px 28px 28px 4px;
  display: flex;
  flex-direction: column;
  gap: 18px;
}

.counter-row {
  display: flex;
  align-items: center;
  justify-content: space-between;
}

.counter-row span:first-child {
  font-size: 13.5px;
  color: var(--color-ink);
  font-weight: 500;
}

.counter {
  display: flex;
  align-items: center;
  gap: 10px;
}

.counter button {
  width: 28px;
  height: 28px;
  border-radius: 25%;
  border: 1px solid var(--color-line);
  background: #fff;
  color: var(--color-pine);
  font-size: 15px;
  line-height: 1;
  cursor: pointer;
  transition: border-color 0.15s ease, background 0.15s ease, opacity 0.15s ease;
}

.counter button:hover:not(:disabled) {
  border-color: var(--color-pine);
  background: var(--color-pine-soft);
}

.counter button:disabled {
  opacity: 0.35;
  cursor: not-allowed;
}

.counter-value {
  width: 16px;
  text-align: center;
  font-weight: 600;
  font-size: 14px;
}

.select-wrap select {
  width: 100px;
  height: 32px;
  border-radius: 16px;
  border: 1px solid var(--color-line);
  background: #fff;
  padding: 0 12px;

  font-size: 13px;
  color: var(--color-ink);
  cursor: pointer;
}

.price-block {
  margin-top: 6px;
  padding-top: 18px;
  border-top: 1px solid var(--color-line);
}

.price-row {
  display: flex;
  align-items: baseline;
  gap: 8px;
  flex-wrap: wrap;
}

.price {

  font-size: 30px;
  font-weight: 600;
  color: var(--color-pine);
}

.price-caption {
  font-size: 11.5px;
  color: var(--color-ink-soft);
}

.price-breakdown {
  margin: 4px 0 0;
  font-size: 12px;
  color: var(--color-gold-deep);
}

.reserve-btn {
  width: 100%;
  height: 46px;
  margin-top: 16px;
  border: none;
  border-radius: 15px;
  background: #3B3B3B;
  color: #fff;
  font-family: "Work Sans", sans-serif;
  font-size: 14px;
  font-weight: 480;
  letter-spacing: 0.2px;
  cursor: pointer;
  transition: background 0.15s ease, transform 0.15s ease;
}

.reserve-btn:hover {
  background: #021c44;
  transform: translateY(-1px);
}

.options-row {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 24px;
  margin-top: 24px;
}

.option {
  margin-bottom: 0;
}

.facilities-card {
  margin-top: 35px;
  padding: 14px 16px;
  background: #f8f9fb;
  border: 1px solid #e7ebf0;
  border-radius: 12px;
}

.facilities-card h4 {
  font-size: 13px;
  font-weight: 550;
  margin-bottom: 7px;
}

.facilities-card h5 {
  margin: 0 0 12px;
  font-size: 13px;
  font-weight: 600;
  color: var(--color-pine);
  text-transform: uppercase;
  letter-spacing: 0.5px;
}

.facility-grid {
  display: grid;
  grid-template-columns: repeat(5, max-content);
  gap: 10px 20px;
}

.facility-item {
  display: flex;
  align-items: center;
  gap: 6px;
  white-space: nowrap; /* prevents wrapping */
  font-size: 11px;
}

.facility-item i {
  color: var(--color-pine);
  font-size: 11px;
}

.offer-badge {
  display: inline-flex;
  align-items: center;
  gap: 6px;
  margin-bottom: 12px;
  padding: 2px 20px;
  background: #edf8ee;
  color: #1b7a3d;
  border: 1px solid #bfe5cb;
  border-radius: 8px;
  font-size: 12px;
  font-weight: 600;
}

.offer-badge i {
  font-size: 12px;
}

/* RESPONSIVE */

@media (max-width: 1050px) {
  .room-card {
    grid-template-columns: 260px 1fr;
  }

  .divider {
    display: none;
  }

  .room-options {
    grid-column: 1 / -1;
    padding: 0 28px 28px;
    border-top: 1px solid var(--color-line);
    margin-top: 20px;
    flex-direction: row;
    flex-wrap: wrap;
    gap: 24px;
    align-items: flex-end;
  }

  .counter-row {
    flex-direction: column;
    align-items: flex-start;
    gap: 8px;
  }

  .price-block {
    width: 100%;
    border-top: none;
    padding-top: 20px;
  }
}

@media (max-width: 768px) {
  .room-card {
    grid-template-columns: 1fr;
  }

  .room-image-box {
    width: 100%;
    height: 220px;
    border-radius: 20px 20px 0 0;
  }

  .room-details {
    padding: 24px;
  }

  .rooms-section h2 {
    font-size: 34px;
  }

  .room-options {
    padding: 0 24px 24px;
    flex-direction: column;
    align-items: stretch;
  }

  .counter-row {
    flex-direction: row;
    align-items: center;
  }
}

@media (prefers-reduced-motion: reduce) {
  .room-card,
  .reserve-btn,
  .pill,
  .counter button {
    transition: none;
  }
}

@media (max-width: 768px) {
  .options-row {
    grid-template-columns: 1fr;
    gap: 20px;
  }
}
</style>
