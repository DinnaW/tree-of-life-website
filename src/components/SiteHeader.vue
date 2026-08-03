<template>
  <header class="header">
    <div class="header-inner">
      <div class="logo">Tree of Life</div>
      <nav class="header-actions">
          <div class="currency-dropdown">
            <button class="currency-btn" @click="toggleCurrency">
              <span class="flag">{{ selectedCurrency.flag }}</span>
              {{ selectedCurrency.code }}
              <span
                class="arrow"
                :class="{ rotate: showCurrency }"
              >
                ▼
              </span>
            </button>

            <Transition name="fade">
              <div v-if="showCurrency" class="currency-menu">
                <div
                  v-for="item in currencies"
                  :key="item.code"
                  class="currency-item"
                  @click="selectCurrency(item)"
                >
                  <span class="flag">{{ item.flag }}</span>
                  {{ item.code }}
                </div>
              </div>
            </Transition>
          </div>
        
        <span class="packages">PACKAGES</span>
        <span class="help-circle">?</span>
      </nav>
    </div>

    <div class="search-bar" ref="searchBar">

      <div class="date-group">

        <div class="search-field" @click="open('dates', 'checkin')">
          <svg class="icon" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.6">
            <rect x="3" y="5" width="18" height="16" rx="2" />
            <path d="M3 9h18M8 3v4M16 3v4" />
          </svg>
          <div>
            <label>Select Check-in date</label>
            <div class="value">{{ checkIn ? formatDate(checkIn) : "Add date" }}</div>
          </div>
        </div>

        <div class="search-field" @click="open('dates', 'checkout')">
          <svg class="icon" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.6">
            <rect x="3" y="5" width="18" height="16" rx="2" />
            <path d="M3 9h18M8 3v4M16 3v4" />
          </svg>
          <div>
            <label>Select Check-out date</label>
            <div class="value">{{ checkOut ? formatDate(checkOut) : "Add date" }}</div>
          </div>
        </div>

        <Transition name="fade">
          <DateRangePicker
            v-if="activePopover === 'dates'"
            class="popover"
            :check-in="checkIn"
            :check-out="checkOut"
            :target="dateTarget"
            @update:checkIn="checkIn = $event"
            @update:checkOut="checkOut = $event"
            @update:target="dateTarget = $event"
            @close="close"
          />
        </Transition>
      </div>

      <!-- Rooms -->
      <div class="search-field" @click="open('rooms')">
        <svg class="icon" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.6">
          <circle cx="12" cy="8" r="3.4" />
          <path d="M5 20c1.4-4 4-6 7-6s5.6 2 7 6" />
        </svg>
        <div>
          <label>Select Rooms</label>
          <div class="value">{{ rooms }} room{{ rooms > 1 ? "s" : "" }}</div>
        </div>
        <Transition name="fade">
          <RoomsPicker
            v-if="activePopover === 'rooms'"
            class="popover"
            v-model="rooms"
            @close="close"
          />
        </Transition>
      </div>

      <!-- Guests -->
      <div class="search-field search-field-last" @click="open('guests')">
        <svg class="icon" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.6">
          <circle cx="12" cy="8" r="3.4" />
          <path d="M5 20c1.4-4 4-6 7-6s5.6 2 7 6" />
        </svg>
        <div>
          <label>Select Guests</label>
          <div class="value">{{ adults }} adults · {{ children }} children</div>
        </div>
        <Transition name="fade">
          <GuestsPicker
            v-if="activePopover === 'guests'"
            class="popover popover-right"
            :adults="adults"
            :children="children"
            @update:adults="adults = $event"
            @update:children="children = $event"
            @close="close"
          />
        </Transition>
      </div>

      <button class="btn-search" @click="onSearch">Search</button>
    </div>
  </header>
</template>

<script setup>
import { ref, onMounted, onBeforeUnmount } from "vue";
import DateRangePicker from "./DateRangePicker.vue";
import RoomsPicker from "./RoomsPicker.vue";
import GuestsPicker from "./GuestsPicker.vue";

const emit = defineEmits(["search"]);

const searchBar = ref(null);
const activePopover = ref(null); // 'dates' | 'rooms' | 'guests' | null
const dateTarget = ref("checkin"); // which field the date popover is currently editing

const checkIn = ref(new Date(2026, 6, 17));
const checkOut = ref(new Date(2026, 6, 18));
const rooms = ref(1);
const adults = ref(2);
const children = ref(0);

function open(name, target) {
  if (activePopover.value === name && (name !== "dates" || dateTarget.value === target)) {
    close();
    return;
  }
  activePopover.value = name;
  if (name === "dates") {
    dateTarget.value = target || "checkin";
  }
}

function close() {
  activePopover.value = null;
}

function onSearch() {
  close();
  emit("search", {
    checkIn: checkIn.value,
    checkOut: checkOut.value,
    rooms: rooms.value,
    adults: adults.value,
    children: children.value,
  });
}
function formatDate(date) {
  return date.toLocaleDateString("en-US", { weekday: "short", month: "short", day: "numeric" });
}

function handleClickOutside(event) {
  if (searchBar.value && !searchBar.value.contains(event.target)) {
    close();
  }

  if (!event.target.closest(".currency-dropdown")) {
    showCurrency.value = false;
  }
}
onMounted(() => document.addEventListener("mousedown", handleClickOutside));
onBeforeUnmount(() => document.removeEventListener("mousedown", handleClickOutside));

const currencies = [
  {
    code: "USD",
    flag: "🇺🇸"
  },
  {
    code: "LKR",
    flag: "🇱🇰"
  }
];

const selectedCurrency = ref(currencies[0]);
const showCurrency = ref(false);

function toggleCurrency() {
  showCurrency.value = !showCurrency.value;
}

function selectCurrency(item) {
  selectedCurrency.value = item;
  showCurrency.value = false;
}

</script>

<style scoped>
.header {
  background: linear-gradient(135deg, #1A51AD 0%, #083377 100%);
  padding: max(26px, 1.8056vw) max(34px, 2.3611vw) 0;
}
.header-inner {
  max-width: max(1240px, 86.1111vw);
  margin: 0 auto;
  display: flex;
  justify-content: space-between;
  align-items: center;
  color: #fff;
}
.logo {
  font-size: max(25px, 1.7361vw);
  font-weight: 550;
  letter-spacing: max(0.2px, 0.0139vw);
  
}
.header-actions {
  display: flex;
  align-items: center;
  gap: max(22px, 1.5278vw);
  font-size: max(14px, 0.9722vw);
  font-weight: 500;
}
.help-circle {
  width: max(22px, 1.5278vw);
  height: max(22px, 1.5278vw);
  border: max(1.5px, 0.1042vw) solid rgba(255, 255, 255, 0.8);
  border-radius: 50%;
  display: inline-flex;
  align-items: center;
  justify-content: center;
  font-size: max(12px, 0.8333vw);
}
.packages {
  letter-spacing: max(0.5px, 0.0347vw);
  font-weight: 450;
}

.search-bar {
  max-width: max(1240px, 86.1111vw);
  margin: max(22px, 1.5278vw) auto 0;
  position: relative;
  top: max(30px, 2.0833vw);
  background: #fff;
  border-radius: max(10px, 0.6944vw);
  box-shadow: 0 max(10px, 0.6944vw) max(30px, 2.0833vw) rgba(20, 40, 70, 0.18);
  display: flex;
  align-items: stretch;
  overflow: visible;
}

.date-group {
  flex: 2;
  display: flex;
  position: relative;
}
.date-group .search-field {
  flex: 1;
}

.search-field {
  flex: 1;
  display: flex;
  align-items: center;
  gap: max(12px, 0.8333vw);
  padding: max(14px, 0.9722vw) max(22px, 1.5278vw);
  border-right: max(1px, 0.0694vw) solid #ececec;
  cursor: pointer;
  position: relative;
}
.search-field:first-child {
  border-radius: max(10px, 0.6944vw) 0 0 max(10px, 0.6944vw);
}
.search-field:hover {
  background: #fafbfd;
}
.search-field .icon {
  width: max(20px, 1.3889vw);
  height: max(20px, 1.3889vw);
  color: #8a8a8a;
  flex-shrink: 0;
}
.search-field label {
  display: block;
  font-size: max(11.5px, 0.7986vw);
  color: #8a8a8a;
  margin-bottom: max(3px, 0.2083vw);
  cursor: pointer;
}
.search-field .value {
  font-size: max(14.5px, 1.0069vw);
  font-weight: 600;
  color: #4a4848;
}
.btn-search {
  background: #0179D7;
  color: #fff;
  border: none;
  padding: 0 max(38px, 2.6389vw);
  font-weight: 500;
  font-size: max(15px, 1.0417vw);
  cursor: pointer;
  border-radius: 0 max(10px, 0.6944vw) max(10px, 0.6944vw) 0;
  transition: background-color 0.25s ease, transform 0.2s ease,
    box-shadow 0.25s ease;
}

.btn-search:hover {
  background: #048cf5;
  box-shadow: 0 max(8px, 0.5556vw) max(18px, 1.25vw) rgba(45, 106, 220, 0.25);
  transform: translateY(min(-1px, -0.0694vw));
}

.popover {
  position: absolute;
  top: calc(100% + max(8px, 0.5556vw));
  left: 0;
  z-index: 200;
}

.popover-right {
  left: auto;
  right: 0;
}

.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.12s ease, transform 0.12s ease;
}
.fade-enter-from,
.fade-leave-to {
  opacity: 0;
  transform: translateY(min(-4px, -0.2778vw));
}

.currency-dropdown {
  position: relative;
}

.currency-btn {
  background: transparent;
  border: none;
  color: white;
  cursor: pointer;
  display: flex;
  align-items: center;
  gap: max(6px, 0.4167vw);
  font-size: max(14px, 0.9722vw);
  font-weight: 450;
}

.flag {
  font-size: max(18px, 1.25vw);
}

.currency-btn .rotate,
.currency-btn .arrow {
  font-size: max(10px, 0.6944vw);
  transition: transform .2s;
}

.currency-btn span.rotate {
  transform: rotate(180deg);
}

.currency-menu {
  position: absolute;
  top: max(32px, 2.2222vw);
  left: 0;
  width: max(80px, 5.5556vw);
  background: white;
  border-radius: max(8px, 0.5556vw);
  box-shadow: 0 max(8px, 0.5556vw) max(24px, 1.6667vw) rgba(0,0,0,.15);
  overflow: hidden;
  z-index: 500;
}

.currency-item {
  display: flex;
  align-items: center;
  gap: max(10px, 0.6944vw);
  padding: max(10px, 0.6944vw) max(14px, 0.9722vw);
  color: #333;
  cursor: pointer;
  transition: background .2s;
}

.currency-item:hover {
  background: #f5f5f5;
}
</style>