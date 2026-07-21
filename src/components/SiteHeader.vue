<template>
  <header class="header">
    <div class="header-inner">
      <div class="logo">Tree of Life</div>
      <nav class="header-actions">
        <span>USD</span>
        <span>🇺🇸</span>
        <span class="help-circle">?</span>
        <span class="packages">PACKAGES</span>
      </nav>
    </div>

    <div class="search-bar" ref="searchBar">
      <!-- Check-in -->
      <div class="search-field" @click="open('dates', $event, 'checkin')">
        <svg class="icon" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.6">
          <rect x="3" y="5" width="18" height="16" rx="2" />
          <path d="M3 9h18M8 3v4M16 3v4" />
        </svg>
        <div>
          <label>Select Check-in date</label>
          <div class="value">{{ checkIn ? formatDate(checkIn) : "Add date" }}</div>
        </div>
      </div>

      <!-- Check-out -->
      <div class="search-field" @click="open('dates', $event, 'checkout')">
        <svg class="icon" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.6">
          <rect x="3" y="5" width="18" height="16" rx="2" />
          <path d="M3 9h18M8 3v4M16 3v4" />
        </svg>
        <div>
          <label>Select Check-out date</label>
          <div class="value">{{ checkOut ? formatDate(checkOut) : "Add date" }}</div>
        </div>
        <Transition name="fade">
          <DateRangePicker
            v-if="activePopover === 'dates'"
            class="popover"
            :style="popoverStyle"
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
      <div class="search-field" @click="open('rooms', $event)">
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
            :style="popoverStyle"
            v-model="rooms"
            @close="close"
          />
        </Transition>
      </div>

      <!-- Guests -->
      <div class="search-field search-field-last" @click="open('guests', $event)">
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
            class="popover"
            :style="popoverStyle"
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
import { ref, nextTick, onMounted, onBeforeUnmount } from "vue";
import DateRangePicker from "./DateRangePicker.vue";
import RoomsPicker from "./RoomsPicker.vue";
import GuestsPicker from "./GuestsPicker.vue";

const emit = defineEmits(["search"]);

const searchBar = ref(null);
const activePopover = ref(null); // 'dates' | 'rooms' | 'guests' | null
const popoverStyle = ref({});
const dateTarget = ref("checkin"); // which field the date popover is currently editing

const checkIn = ref(new Date(2026, 6, 17));
const checkOut = ref(new Date(2026, 6, 18));
const rooms = ref(1);
const adults = ref(2);
const children = ref(0);

function open(name, event, target) {
  if (activePopover.value === name && (name !== "dates" || dateTarget.value === target)) {
    close();
    return;
  }
  activePopover.value = name;
  if (name === "dates") {
    dateTarget.value = target || "checkin";
  }
  const fieldEl = event.currentTarget;
  nextTick(() => positionPopover(fieldEl));
}

function positionPopover(fieldEl) {
  const rect = fieldEl.getBoundingClientRect();
  const gap = 8;
  const viewportH = window.innerHeight;
  const viewportW = window.innerWidth;
  // Reserve room for a ~380px-tall popover; flip upward if there's more
  // space above the field than below it.
  const estimatedHeight = 380;
  const spaceBelow = viewportH - rect.bottom;
  const openUpward = spaceBelow < estimatedHeight && rect.top > spaceBelow;

  const style = {
    position: "fixed",
    zIndex: 200,
  };
  if (openUpward) {
    style.bottom = `${viewportH - rect.top + gap}px`;
    style.maxHeight = `${rect.top - gap - 8}px`;
  } else {
    style.top = `${rect.bottom + gap}px`;
    style.maxHeight = `${viewportH - rect.bottom - gap - 8}px`;
  }
  // Keep the popover from overflowing the right edge of the window
  const popoverWidth = 300;
  const left = Math.min(rect.left, viewportW - popoverWidth - 12);
  style.left = `${Math.max(12, left)}px`;

  popoverStyle.value = style;
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
}
onMounted(() => document.addEventListener("mousedown", handleClickOutside));
onBeforeUnmount(() => document.removeEventListener("mousedown", handleClickOutside));
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

.popover {
  z-index: 200;
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


</style>
