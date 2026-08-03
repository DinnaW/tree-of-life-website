<template>
  <section class="rooms-section">
    <div class="section-header">
      <div class="eyebrow">— ROOMS</div>
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
        <img :src="room.image" :alt="room.name" class="room-image" 
        @click="openRoomDetails(room)" />
        <span class="room-tag">{{ room.tag }}</span>
      </div>

      <!-- DETAILS -->
      <div class="room-details">
        <div v-if="room.offer" class="offer-badge">
            <i class="fa-solid fa-tags"></i>
            {{ room.offer }}
          </div>
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

        <div class="facilities-card" :class="{ expanded: expandedFacilities[room.id] }">
          <div class="facility-grid">
            <div
              class="facility-item"
              v-for="facility in visibleFacilities(room)"
              :key="facility.name"
            >
              <i :class="facility.icon"></i>
              <span>{{ facility.name }}</span>
            </div>
          </div>

          <button
            class="see-more-btn"
            type="button"
            @click="openRoomDetails(room)"
            >
            See More
          </button>
        </div>
      </div>

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

          <div class="price-row">
            <span v-if="room.originalPrice" class="price-original">${{ originalTotal(room) }}</span>
            <span class="price">${{ total(room) }}</span>
            <span class="price-caption">
              total for {{ nights }} {{ nights === 1 ? "night" : "nights" }} · taxes & fees included
            </span>
          </div>
          
          <button class="reserve-btn" @click="reserveFromCard(room)">Reserve this room</button>
        </div>
      </div>
    </article>
  </section>
  <RoomDetailsModal
  :is-open="isRoomModalOpen"
  :room="selectedRoom"
  :nights="nights"
  :extra-bed-fee="EXTRA_BED_FEE"
  @close="closeRoomDetails"
  @reserve="handleRoomReservation"
/>
</template>

<script setup>
import { ref, computed } from "vue";
import AvailabilityBar from "./AvailabilityBar.vue";
import RoomDetailsModal from "./modals/RoomDetailsModal.vue";

const emit = defineEmits(["reserve"]);

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
    images: [
      `${baseUrl}images/room1.jpg`,
      `${baseUrl}images/room1-2.jpg`,
      `${baseUrl}images/room1-3.jpg`,
      `${baseUrl}images/room1-4.jpg`
    ],
    tag: "Panoramic view",
    meta: "38 m² · King bed · Private balcony",
    price: 78,
    subtitle: "Panoramic mountain retreat",
    guests: 2,
    size: "38 m²",
    view: "Panoramic mountain view",

    description:
      "A spacious deluxe room with warm natural interiors, a king-size bed, private balcony, and beautiful panoramic views. Ideal for couples seeking a quiet and comfortable stay.",

    bathroomFacilities: [
      "Private bathroom",
      "Hot water",
      "Walk-in shower",
      "Hair dryer",
      "Fresh towels"
    ],

    included: [
      "Daily breakfast",
      "Free WiFi",
      "Free parking",
      "Tea and coffee facilities"
    ],

    checkIn: "From 2:00 PM",
    checkOut: "Until 11:00 AM",
    smoking: "Non-smoking room",
    bed: "Single",
    meal: "Bed & Breakfast",
    cancellation: "Free cancellation up to 48 hours before check-in",

    children: 0,
    extraBeds: 0,
    roomCount: 1,

    facilities: [
      { icon: "fa-solid fa-wifi", name: "Free WiFi" },
      { icon: "fa-solid fa-tv", name: "Smart TV" },
      { icon: "fa-solid fa-paw", name: "Pet Friendly" },
      { icon: "fa-solid fa-mug-hot", name: "Tea/Coffee" },
    
    ]
  },
  {
    id: 2,
    mostBooked: false,
    name: "Green Zone Deluxe",
    image: `${baseUrl}images/room2.jpg`,
    images: [
      `${baseUrl}images/room2.jpg`,
      `${baseUrl}images/room2-2.jpg`,
      `${baseUrl}images/room2-3.jpg`,
      `${baseUrl}images/room2-4.jpg`
    ],
    tag: "Garden view",
    meta: "34 m² · Canopy bed · Private terrace",
    price: 105,
    subtitle: "Peaceful garden escape",
    guests: 2,
    size: "34 m²",
    view: "Private garden view",
    offer: "Save 20% Today",
    description:
      "A tranquil deluxe room surrounded by greenery, featuring a canopy bed, private terrace, and direct views of the resort garden.",

    bathroomFacilities: [
      "Private bathroom",
      "Hot water",
      "Rain shower",
      "Hair dryer",
      "Fresh towels"
    ],

    included: [
      "Daily breakfast",
      "Free WiFi",
      "Free parking",
      "Room service"
    ],

    checkIn: "From 2:00 PM",
    checkOut: "Until 11:00 AM",
    smoking: "Non-smoking room",
    roomsLeft: 1,
    bed: "Single",
    meal: "Bed & Breakfast",
    offer: "Save 20% Today",
    cancellation: "Non-refundable — this rate cannot be changed or cancelled",

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
      
    ]
  },
  {

    id: 3,
    mostBooked: false,
    name: "Panoramic Deluxe",
    image: `${baseUrl}images/room3.jpg`,
    images: [
      `${baseUrl}images/room3.jpg`,
      `${baseUrl}images/room3-2.jpg`,
      `${baseUrl}images/room3-3.jpg`,
      `${baseUrl}images/room3-4.jpg`
    ],
    tag: "Panoramic view",
    meta: "38 m² · King bed · Private balcony",
    price: 78,
    subtitle: "Limited-time panoramic stay",
    guests: 2,
    size: "38 m²",
    view: "Panoramic mountain view",
    offer: "Limited Time Offer",
    description:
      "A comfortable panoramic deluxe room with a king-size bed, private balcony, and a special limited-time rate.",

    bathroomFacilities: [
      "Private bathroom",
      "Hot water",
      "Walk-in shower",
      "Hair dryer",
      "Fresh towels"
    ],

    included: [
      "Daily breakfast",
      "Free WiFi",
      "Free parking",
      "Tea and coffee facilities"
    ],

    checkIn: "From 2:00 PM",
    checkOut: "Until 11:00 AM",
    smoking: "Non-smoking room",
    originalPrice: 98,
    offer: "Limited Time Offer",
    cancellation: "Non-refundable — this rate cannot be changed or cancelled",

    bed: "Single",
    meal: "Bed & Breakfast",

    children: 0,
    extraBeds: 0,
    roomCount: 1,

    facilities: [
      { icon: "fa-solid fa-wifi", name: "Free WiFi" },
      { icon: "fa-solid fa-tv", name: "Smart TV" },
      { icon: "fa-solid fa-paw", name: "Pet Friendly" },
      { icon: "fa-solid fa-mug-hot", name: "Tea/Coffee" },
    ]
  }
]);

function decrement(room, key) {
  room[key] = Math.max(0, room[key] - 1);
}

const EXTRA_BED_FEE = 15;

const nights = computed(() => {
  if (!startDate.value || !endDate.value) return 1;

  const start = new Date(startDate.value);
  const end = new Date(endDate.value);
  const diffDays = Math.round((end - start) / (1000 * 60 * 60 * 24));

  return diffDays > 0 ? diffDays : 1;
});

function total(room) {
  return room.price * room.roomCount * nights.value + room.extraBeds * EXTRA_BED_FEE;
}

function originalTotal(room) {
  return room.originalPrice * room.roomCount * nights.value + room.extraBeds * EXTRA_BED_FEE;
}

const VISIBLE_FACILITY_COUNT = 4;
const expandedFacilities = ref({});

function visibleFacilities(room) {
  return expandedFacilities.value[room.id]
    ? room.facilities
    : room.facilities.slice(0, VISIBLE_FACILITY_COUNT);
}

const selectedRoom = ref(null);
const isRoomModalOpen = ref(false);

function openRoomDetails(room) {
  selectedRoom.value = room;
  isRoomModalOpen.value = true;
}

function closeRoomDetails() {
  isRoomModalOpen.value = false;
  selectedRoom.value = null;
}

function reserveFromCard(room) {
  emit("reserve", { room, nights: nights.value });
}

function handleRoomReservation(room) {
  closeRoomDetails();
  emit("reserve", { room, nights: nights.value });
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

  max-width: max(1340px, 93.0556vw);
  padding: max(50px, 3.4722vw) max(50px, 3.4722vw);
  background:  #f8f9fb;
  color: var(--color-ink);
}

/* SECTION HEAD */

.section-head {
  margin-bottom: max(48px, 3.3333vw);
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

.rooms-section h2 {
  font-size:max(34px, 2.3611vw);
  font-weight:500;
  color:#1A51AD;
  margin-bottom:max(55px, 3.8194vw);
  line-height:1.3;
}

.subhead {
  margin-bottom: max(20px, 1.3889vw);
  font-size: max(12px, 0.8333vw);
  color: var(--color-ink-soft);
}

/* ROOM CARD */

.room-card {
  position: relative;
  display: grid;
  grid-template-columns: max(300px, 20.8333vw) 1fr max(24px, 1.6667vw) max(260px, 18.0556vw);
  align-items: stretch;
  gap: 0;
  background: var(--color-surface);
  border-radius: max(20px, 1.3889vw);
  margin-bottom: max(28px, 1.9444vw);
  box-shadow: 0 max(1px, 0.0694vw) max(2px, 0.1389vw) rgba(31, 46, 36, 0.04);
  transition: box-shadow 0.25s ease, transform 0.25s ease;
  
}

.room-card:hover {
  box-shadow: 0 max(18px, 1.25vw) max(40px, 2.7778vw) rgba(31, 46, 36, 0.1);
  transform: translateY(min(-2px, -0.1389vw));
}

/* IMAGE */

.room-image-box {
  cursor: pointer;
  position: relative;
  width: 100%;
  height: 100%;
  min-height: max(280px, 19.4444vw);
  overflow: hidden;
  border-radius: max(20px, 1.3889vw) 0 0 max(20px, 1.3889vw);
}

.room-image {
  width: 100%;
  height: 100%;
  object-fit: cover;
  display: block;
}

.room-tag {
  position: absolute;
  left: max(14px, 0.9722vw);
  bottom: max(14px, 0.9722vw);
  padding: max(6px, 0.4167vw) max(14px, 0.9722vw);
  border-radius: max(20px, 1.3889vw);
  background: rgba(68, 68, 68, 0.55);
  backdrop-filter: blur(max(6px, 0.4167vw));
  color: #fff;
  font-size: max(12px, 0.8333vw);
  font-weight: 500;
  letter-spacing: max(0.3px, 0.0208vw);
}

/* ROOM DETAILS */
.room-title {
  display: flex;
  align-items: center;
  gap: max(12px, 0.8333vw);
  margin-bottom: max(6px, 0.4167vw);
}

.room-title h3 {
  margin: 0;
  font-size: max(26px, 1.8056vw);
  font-weight: 500;
  color: var(--color-pine);
}

.badge {
  display: inline-flex;
  align-items: center;
  justify-content: center;
  height: max(28px, 1.9444vw);
  padding: 0 max(16px, 1.1111vw);
  background: #eef3fa;
  color: #1a51ad;
  font-size: max(11px, 0.7639vw);
  font-weight: 600;
  letter-spacing: max(1px, 0.0694vw);
  text-transform: uppercase;
  border-radius: max(4px, 0.2778vw);
}

.room-details {
  padding: max(32px, 2.2222vw) max(32px, 2.2222vw) max(32px, 2.2222vw) max(30px, 2.0833vw);
}

.room-details h3 {
  margin: 0 0 max(6px, 0.4167vw);
 
  font-size: max(26px, 1.8056vw);
  font-weight: 500;
  color: var(--color-pine);
}

.room-meta {
  margin: 0 0 max(26px, 1.8056vw);
  font-size: max(13.5px, 0.9375vw);
  color: var(--color-ink-soft);
}

.option {
  margin-bottom: 0;
}

.option label {
  display: block;
  font-size: max(12.5px, 0.8681vw);
  color: var(--color-ink-soft);
  font-weight: 600;
  letter-spacing: max(0.4px, 0.0278vw);
  text-transform: uppercase;
  margin-bottom: max(10px, 0.6944vw);
}

.rooms-left {
  margin-bottom: max(10px, 0.6944vw);
  color: rgb(242, 56, 56);
  font-size: max(13px, 0.9028vw);
  font-weight: 600;
}

/* PILL BUTTONS */

.pill-group {
  display: flex;
  gap: max(8px, 0.5556vw);
  flex-wrap: wrap;
}

.pill {
  height: max(38px, 2.6389vw);
  padding: 0 max(18px, 1.25vw);
  border-radius: max(10px, 0.6944vw);
  border: max(1px, 0.0694vw) solid var(--color-line);
  background: #fff;
  color: var(--color-ink);
 
  font-size: max(12px, 0.8333vw);
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

.divider {
  position: relative;
  border-left: max(1px, 0.0694vw) dashed var(--color-line);
  margin: max(24px, 1.6667vw) 0;
}

.notch {
  position: absolute;
  left: min(-9px, -0.625vw);
  width: max(18px, 1.25vw);
  height: max(18px, 1.25vw);
  border-radius: 50%;
  background: var(--color-bg);
}

.notch-top {
  top: min(-24px, -1.6667vw);
}

.notch-bottom {
  bottom: min(-24px, -1.6667vw);
}

/* BOOKING PANEL */

.room-options {
  padding: max(28px, 1.9444vw) max(28px, 1.9444vw) max(28px, 1.9444vw) max(4px, 0.2778vw);
  display: flex;
  flex-direction: column;
  gap: max(18px, 1.25vw);
}

.counter-row {
  display: flex;
  align-items: center;
  justify-content: space-between;
}

.counter-row span:first-child {
  font-size: max(13.5px, 0.9375vw);
  color: var(--color-ink);
  font-weight: 500;
}

.counter {
  display: flex;
  align-items: center;
  gap: max(10px, 0.6944vw);
}

.counter button {
  width: max(28px, 1.9444vw);
  height: max(28px, 1.9444vw);
  border-radius: 25%;
  border: max(1px, 0.0694vw) solid var(--color-line);
  background: #fff;
  color: var(--color-pine);
  font-size: max(15px, 1.0417vw);
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
  width: max(16px, 1.1111vw);
  text-align: center;
  font-weight: 600;
  font-size: max(14px, 0.9722vw);
}

.select-wrap select {
  width: max(100px, 6.9444vw);
  height: max(32px, 2.2222vw);
  border-radius: max(16px, 1.1111vw);
  border: max(1px, 0.0694vw) solid var(--color-line);
  background: #fff;
  padding: 0 max(12px, 0.8333vw);

  font-size: max(13px, 0.9028vw);
  color: var(--color-ink);
  cursor: pointer;
}

.price-block {
  margin-top: max(6px, 0.4167vw);
  padding-top: max(18px, 1.25vw);
  border-top: max(1px, 0.0694vw) solid var(--color-line);
}

.price-row {
  display: flex;
  align-items: baseline;
  gap: max(8px, 0.5556vw);
  flex-wrap: wrap;
}

.price {

  font-size: max(30px, 2.0833vw);
  font-weight: 600;
  color: var(--color-pine);
}

.price-original {
  font-size: max(20px, 1.3889vw);
  font-weight: 10;
  color: rgb(242, 56, 56);
  text-decoration: line-through;
}

.price-caption {
  font-size: max(11.5px, 0.7986vw);
  color: var(--color-ink-soft);
}

.price-breakdown {
  margin: max(4px, 0.2778vw) 0 0;
  font-size: max(12px, 0.8333vw);
  color: var(--color-gold-deep);
}

.reserve-btn {
  width: 100%;
  height: max(46px, 3.1944vw);
  margin-top: max(16px, 1.1111vw);
  border: none;
  border-radius: max(15px, 1.0417vw);
  background: #021c44;
  color: #fff;
  font-family: "Work Sans", sans-serif;
  font-size: max(14px, 0.9722vw);
  font-weight: 480;
  letter-spacing: max(0.2px, 0.0139vw);
  cursor: pointer;
  transition: background 0.15s ease, transform 0.15s ease;
}

.reserve-btn:hover {
  background: #032d6b;
  transform: translateY(min(-1px, -0.0694vw));
}

.options-row {
  display: flex;
  flex-direction: column;
  gap: max(20px, 1.3889vw);
  margin-top: max(24px, 1.6667vw);
}

.facilities-card {
  margin-top: max(35px, 2.4306vw);
  padding: max(12px, 0.8333vw) max(16px, 1.1111vw);
  background: #f8f9fb;
  border: max(1px, 0.0694vw) solid #e7ebf0;
  border-radius: max(12px, 0.8333vw);
  display: flex;
  align-items: center;
  gap: max(14px, 0.9722vw);
}

.facilities-card.expanded {
  flex-direction: column;
  align-items: flex-start;
}

.facilities-card h4 {
  font-size: max(13px, 0.9028vw);
  font-weight: 550;
  margin-bottom: max(7px, 0.4861vw);
}

.facilities-card h5 {
  margin: 0 0 max(12px, 0.8333vw);
  font-size: max(13px, 0.9028vw);
  font-weight: 600;
  color: var(--color-pine);
  text-transform: uppercase;
  letter-spacing: max(0.5px, 0.0347vw);
}

.facility-grid {
  display: flex;
  flex-wrap: nowrap;
  overflow: hidden;
  gap: max(20px, 1.3889vw);
  flex: 1;
  min-width: 0;
}

.facilities-card.expanded .facility-grid {
  flex-wrap: wrap;
  overflow: visible;
  width: 100%;
}

.facility-item {
  display: flex;
  align-items: center;
  gap: max(6px, 0.4167vw);
  white-space: nowrap; /* prevents wrapping */
  font-size: max(11px, 0.7639vw);
  flex-shrink: 0;
}

.facility-item i {
  color: var(--color-pine);
  font-size: max(11px, 0.7639vw);
}

.see-more-btn {
  flex-shrink: 0;
  margin-left: auto;
  background: none;
  border: none;
  color: var(--color-pine);
  font-weight: 600;
  font-size: max(11px, 0.7639vw);
  cursor: pointer;
  white-space: nowrap;
  padding: 0;
}

.see-more-btn:hover {
  text-decoration: underline;
}

.facilities-card.expanded .see-more-btn {
  margin-left: 0;
  margin-top: max(12px, 0.8333vw);
}

.offer-badge {
  display: inline-flex;
  align-items: center;
  gap: max(6px, 0.4167vw);
  margin-bottom: max(12px, 0.8333vw);
  padding: max(2px, 0.1389vw) max(20px, 1.3889vw);
  background: #edf8ee;
  color: #1b7a3d;
  border: max(1px, 0.0694vw) solid #bfe5cb;
  border-radius: max(8px, 0.5556vw);
  font-size: max(12px, 0.8333vw);
  font-weight: 600;
}

.offer-badge i {
  font-size: max(12px, 0.8333vw);
}

/* RESPONSIVE */

@media (max-width: 1200px) {
  .rooms-section {
    padding: 50px 30px;
  }

  .room-card {
    grid-template-columns: 260px 1fr 24px 220px;
  }

  .room-details {
    padding: 26px 24px 26px 22px;
  }
}

@media (max-width: 1050px) {
  .room-card {
    grid-template-columns: 260px 1fr;
  }

  .divider {
    display: none;
  }

  .room-options {
    grid-column: 1 / -1;
    margin: 20px 28px 0;
    padding: 24px 0 28px;
    border-top: 1px dashed var(--color-line);
    display: flex;
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
    font-size: 26px;
  }

  .room-options {
    margin: 20px 24px 0;
    padding: 32px 0 24px;
    flex-direction: column;
    align-items: stretch;
    gap: 24px;
  }

  .counter-row {
    flex-direction: row;
    align-items: center;
  }
}

/* SMALL PHONES */
@media (max-width: 480px) {
  .rooms-section {
    padding: 40px 16px;
  }

  .rooms-section h2 {
    font-size: 22px;
  }

  .room-title h3 {
    font-size: 21px;
  }

  .room-details {
    padding: 20px;
  }

  .price {
    font-size: 26px;
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