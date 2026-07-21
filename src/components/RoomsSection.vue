<template>
  <section class="rooms-section">
    <header class="section-head">
      
      <div class="eyebrow">
        03 — ROOMS
      </div>

      <h2>Choose your room</h2>

      <p class="subhead">
        {{ rooms.length }} room types · flexible bed and board plans, priced per room
      </p>
    </header>

    <article class="room-card" v-for="room in rooms" :key="room.id">
      <!-- IMAGE -->
      <div class="room-image-box">
        <img :src="room.image" :alt="room.name" class="room-image" />
        <span class="room-tag">{{ room.tag }}</span>
      </div>

      <!-- DETAILS -->
      <div class="room-details">
        <h3>{{ room.name }}</h3>
        <p class="room-meta">{{ room.meta }}</p>

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
          <div class="price-row">
            <span class="price">${{ total(room) }}</span>
            <span class="price-caption">total · taxes &amp; fees included</span>
          </div>
          <p class="price-breakdown" v-if="room.extraBeds > 0">
            incl. {{ room.extraBeds }} extra bed{{ room.extraBeds > 1 ? 's' : '' }}
          </p>
          <button type="button" class="reserve-btn">Reserve this room</button>
        </div>
      </div>
    </article>
  </section>
</template>

<script setup>
import { ref } from "vue";

const baseUrl = import.meta.env.BASE_URL;

const rooms = ref([
  {
    id: 1,
    name: "Panoramic Deluxe",
    image: `${baseUrl}images/room1.jpg`,
    tag: "Panoramic view",
    meta: "38 m² · King bed · Private balcony",
    price: 78,

    bed: "Single",
    meal: "Bed & Breakfast",

    children: 0,
    extraBeds: 0,
    roomCount: 1
  },
  {
    id: 2,
    name: "Green Zone Deluxe",
    image: `${baseUrl}images/room2.jpg`,
    tag: "Garden view",
    meta: "34 m² · Canopy bed · Private terrace",
    price: 78,

    bed: "Single",
    meal: "Bed & Breakfast",

    children: 0,
    extraBeds: 0,
    roomCount: 1
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

  max-width: 1200px;
  padding: 50px 20px;
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
  margin: 0;
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
</style>
