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

      <!-- Check-in + Check-out share one relatively-positioned group so a
           single DateRangePicker can anchor below both fields, exactly
           like AvailabilityBar's .field / .calendar-dropdown pattern. -->
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
  padding: 26px 34px 0;
}
.header-inner {
  max-width: 1240px;
  margin: 0 auto;
  display: flex;
  justify-content: space-between;
  align-items: center;
  color: #fff;
}
.logo {
  font-size: 25px;
  font-weight: 600;
  letter-spacing: 0.2px;
  
}
.header-actions {
  display: flex;
  align-items: center;
  gap: 22px;
  font-size: 14px;
  font-weight: 500;
}
.help-circle {
  width: 22px;
  height: 22px;
  border: 1.5px solid rgba(255, 255, 255, 0.8);
  border-radius: 50%;
  display: inline-flex;
  align-items: center;
  justify-content: center;
  font-size: 12px;
}
.packages {
  letter-spacing: 0.5px;
  font-weight: 500;
}

.search-bar {
  max-width: 1240px;
  margin: 22px auto 0;
  position: relative;
  top: 30px;
  background: #fff;
  border-radius: 10px;
  box-shadow: 0 10px 30px rgba(20, 40, 70, 0.18);
  display: flex;
  align-items: stretch;
  overflow: visible;
}

/* Wraps Check-in + Check-out so the shared DateRangePicker has one
   relatively-positioned ancestor to anchor below, spanning both fields. */
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
  gap: 12px;
  padding: 14px 22px;
  border-right: 1px solid #ececec;
  cursor: pointer;
  position: relative;
}
.search-field:first-child {
  border-radius: 10px 0 0 10px;
}
.search-field:hover {
  background: #fafbfd;
}
.search-field .icon {
  width: 20px;
  height: 20px;
  color: #8a8a8a;
  flex-shrink: 0;
}
.search-field label {
  display: block;
  font-size: 11.5px;
  color: #8a8a8a;
  margin-bottom: 3px;
  cursor: pointer;
}
.search-field .value {
  font-size: 14.5px;
  font-weight: 600;
  color: #4a4848;
}
.btn-search {
  background: #0179D7;
  color: #fff;
  border: none;
  padding: 0 38px;
  font-weight: 500;
  font-size: 15px;
  cursor: pointer;
  border-radius: 0 10px 10px 0;
  transition: background-color 0.25s ease, transform 0.2s ease,
    box-shadow 0.25s ease;
}

.btn-search:hover {
  background: #048cf5;
  box-shadow: 0 8px 18px rgba(45, 106, 220, 0.25);
  transform: translateY(-1px);
}

/* Simple, scroll-friendly popover anchoring — matches AvailabilityBar's
   .field / .calendar-dropdown pattern instead of JS-computed fixed
   positioning, so it scrolls naturally with the page and never detaches
   from its field. */
.popover {
  position: absolute;
  top: calc(100% + 8px);
  left: 0;
  z-index: 200;
}

/* Anchor the last field's popover (Guests) to the right edge so it
   doesn't overflow past the search bar. */
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
  transform: translateY(-4px);
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
  gap: 6px;
  font-size: 14px;
  font-weight: 500;
}

.flag {
  font-size: 18px;
}

.currency-btn .rotate,
.currency-btn .arrow {
  font-size: 10px;
  transition: transform .2s;
}

.currency-btn span.rotate {
  transform: rotate(180deg);
}

.currency-menu {
  position: absolute;
  top: 32px;
  left: 0;
  width: 80px;
  background: white;
  border-radius: 8px;
  box-shadow: 0 8px 24px rgba(0,0,0,.15);
  overflow: hidden;
  z-index: 500;
}

.currency-item {
  display: flex;
  align-items: center;
  gap: 10px;
  padding: 10px 14px;
  color: #333;
  cursor: pointer;
  transition: background .2s;
}

.currency-item:hover {
  background: #f5f5f5;
}
</style>