<template>
  <header class="header">
    <div class="header-inner">
  <div
  class="logo"
  @click="emit('show-home')"
>
  Tree of Life
</div>
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
        
        <button
          type="button"
          class="packages-link"
          @click="emit('show-packages')"
        >
          PACKAGES
        </button>

        <span class="help-circle">?</span>
      </nav>
    </div>

    <div class="search-bar" ref="searchBar">
      <div class="booking-type-field">
        <label>I'm looking for</label>
        <BookingTypeSwitch
          compact
          :model-value="bookingType"
          @update:model-value="emit('update:bookingType', $event)"
        />
      </div>

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
import BookingTypeSwitch from "./BookingTypeSwitch.vue";

const props = defineProps({
  bookingType: {
    type: String,
    default: "rooms"
  }
});

const emit = defineEmits([
  "search",
  "show-home",
  "show-packages",
  "update:bookingType"
]);

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
    bookingType: props.bookingType,
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
  background: linear-gradient(135deg, #1a51ad 0%, #083377 100%);
  padding: max(26px, 1.8056vw) max(34px, 2.3611vw) 0;
}

.header-inner {
  width: 100%;
  max-width: max(1240px, 86.1111vw);
  margin: 0 auto;
  display: flex;
  justify-content: space-between;
  align-items: center;
  color: #ffffff;
}

.logo {
  font-size: max(25px, 1.7361vw);
  font-weight: 600;
  letter-spacing: max(0.2px, 0.0139vw);
  cursor: pointer;
  transition: opacity 0.2s ease;
}

.logo:hover {
  opacity: 0.8;
}

.header-actions {
  display: flex;
  align-items: center;
  gap: max(22px, 1.5278vw);
  font-size: max(14px, 0.9722vw);
  font-weight: 500;
}

.packages-link {
  padding: 0;
  border: 0;
  background: transparent;
  color: #ffffff;
  font: inherit;
  font-size: max(13px, 0.9028vw);
  font-weight: 600;
  letter-spacing: max(0.5px, 0.0417vw);
  cursor: pointer;
  transition: opacity 0.2s ease;
}

.packages-link:hover {
  opacity: 0.78;
}

.help-circle {
  width: max(22px, 1.5278vw);
  height: max(22px, 1.5278vw);
  border: max(1.5px, 0.1042vw) solid rgba(255, 255, 255, 0.8);
  border-radius: 50%;
  display: inline-flex;
  align-items: center;
  justify-content: center;
  flex-shrink: 0;
  font-size: max(12px, 0.8333vw);
}

.search-bar {
  width: 100%;
  max-width: max(1240px, 86.1111vw);
  margin: max(22px, 1.5278vw) auto 0;
  position: relative;
  top: max(30px, 2.0833vw);
  background: #ffffff;
  border-radius: max(10px, 0.6944vw);
  box-shadow:
    0 max(10px, 0.6944vw) max(30px, 2.0833vw)
    rgba(20, 40, 70, 0.18);
  display: flex;
  align-items: stretch;
  overflow: visible;
}

.booking-type-field {
  flex: 0 0 max(190px, 13.1944vw);
  display: flex;
  flex-direction: column;
  justify-content: center;
  gap: max(6px, 0.4167vw);
  padding: max(10px, 0.6944vw) max(14px, 0.9722vw);
  border-right: max(1px, 0.0694vw) solid #ececec;
  border-radius:
    max(10px, 0.6944vw)
    0
    0
    max(10px, 0.6944vw);
}

.booking-type-field > label {
  color: #8a8a8a;
  font-size: max(10.5px, 0.7292vw);
  font-weight: 500;
}

.date-group {
  flex: 2;
  display: flex;
  position: relative;
  min-width: 0;
}

.date-group .search-field {
  flex: 1;
}

.search-field {
  flex: 1;
  min-width: 0;
  display: flex;
  align-items: center;
  gap: max(12px, 0.8333vw);
  padding:
    max(14px, 0.9722vw)
    max(22px, 1.5278vw);
  border-right: max(1px, 0.0694vw) solid #ececec;
  cursor: pointer;
  position: relative;
  transition: background-color 0.2s ease;
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

.search-field > div {
  min-width: 0;
}

.search-field label {
  display: block;
  margin-bottom: max(3px, 0.2083vw);
  color: #8a8a8a;
  font-size: max(11.5px, 0.7986vw);
  cursor: pointer;
  white-space: nowrap;
}

.search-field .value {
  color: #4a4848;
  font-size: max(14.5px, 1.0069vw);
  font-weight: 600;
  white-space: nowrap;
}

.btn-search {
  flex-shrink: 0;
  background: #0179d7;
  color: #ffffff;
  border: none;
  padding: 0 max(38px, 2.6389vw);
  font-weight: 500;
  font-size: max(15px, 1.0417vw);
  cursor: pointer;
  border-radius:
    0
    max(10px, 0.6944vw)
    max(10px, 0.6944vw)
    0;
  transition:
    background-color 0.25s ease,
    transform 0.2s ease,
    box-shadow 0.25s ease;
}

.btn-search:hover {
  background: #048cf5;
  box-shadow:
    0 max(8px, 0.5556vw) max(18px, 1.25vw)
    rgba(45, 106, 220, 0.25);
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
  transition:
    opacity 0.12s ease,
    transform 0.12s ease;
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
  padding: 0;
  background: transparent;
  border: none;
  color: #ffffff;
  cursor: pointer;
  display: flex;
  align-items: center;
  gap: max(6px, 0.4167vw);
  font-size: max(14px, 0.9722vw);
  font-weight: 500;
}

.flag {
  font-size: max(18px, 1.25vw);
  line-height: 1;
}

.currency-btn .rotate,
.currency-btn .arrow {
  font-size: max(10px, 0.6944vw);
  transition: transform 0.2s ease;
}

.currency-btn span.rotate {
  transform: rotate(180deg);
}

.currency-menu {
  position: absolute;
  top: max(32px, 2.2222vw);
  left: 0;
  width: max(80px, 5.5556vw);
  background: #ffffff;
  border-radius: max(8px, 0.5556vw);
  box-shadow:
    0 max(8px, 0.5556vw) max(24px, 1.6667vw)
    rgba(0, 0, 0, 0.15);
  overflow: hidden;
  z-index: 500;
}

.currency-item {
  display: flex;
  align-items: center;
  gap: max(10px, 0.6944vw);
  padding:
    max(10px, 0.6944vw)
    max(14px, 0.9722vw);
  color: #333333;
  cursor: pointer;
  transition: background-color 0.2s ease;
}

.currency-item:hover {
  background: #f5f5f5;
}

@media (max-width: 1024px) {
  .header {
    padding-inline: max(24px, 3.3203vw);
  }

  .search-bar {
    flex-wrap: wrap;
  }

  .booking-type-field {
    flex: 1 0 100%;
    border-right: 0;
    border-bottom: max(1px, 0.0977vw) solid #ececec;
    border-radius:
      max(10px, 0.9766vw)
      max(10px, 0.9766vw)
      0
      0;
  }

  .date-group {
    flex: 1 0 50%;
  }

  .search-field {
    min-height: max(68px, 6.6406vw);
  }

  .btn-search {
    min-height: max(68px, 6.6406vw);
  }
}

@media (max-width: 720px) {
  .header {
    padding:
      max(20px, 5.5556vw)
      max(18px, 5vw)
      0;
  }

  .header-inner {
    gap: max(14px, 3.8889vw);
  }

  .logo {
    font-size: max(21px, 5.8333vw);
  }

  .header-actions {
    gap: max(10px, 2.7778vw);
  }

  .packages-link {
    font-size: max(11px, 3.0556vw);
  }

  .currency-dropdown {
    display: none;
  }

  .search-bar {
    top: max(24px, 6.6667vw);
    flex-direction: column;
    flex-wrap: nowrap;
    border-radius: max(10px, 2.7778vw);
  }

  .booking-type-field {
    flex: none;
    width: 100%;
    padding:
      max(12px, 3.3333vw)
      max(16px, 4.4444vw);
    border-right: 0;
    border-bottom: max(1px, 0.2778vw) solid #ececec;
    border-radius:
      max(10px, 2.7778vw)
      max(10px, 2.7778vw)
      0
      0;
  }

  .date-group {
    flex: none;
    width: 100%;
  }

  .date-group .search-field {
    width: 50%;
  }

  .search-field {
    flex: none;
    width: 100%;
    min-width: 0;
    min-height: max(64px, 17.7778vw);
    padding:
      max(12px, 3.3333vw)
      max(16px, 4.4444vw);
    border-right: 0;
    border-bottom: max(1px, 0.2778vw) solid #ececec;
  }

  .date-group .search-field:first-child {
    border-right: max(1px, 0.2778vw) solid #ececec;
  }

  .search-field label {
    font-size: max(10px, 2.7778vw);
  }

  .search-field .value {
    font-size: max(13px, 3.6111vw);
    white-space: normal;
  }

  .search-field .icon {
    width: max(18px, 5vw);
    height: max(18px, 5vw);
  }

  .btn-search {
    width: 100%;
    min-height: max(56px, 15.5556vw);
    border-radius:
      0
      0
      max(10px, 2.7778vw)
      max(10px, 2.7778vw);
  }

  .popover {
    max-width: calc(100vw - max(36px, 10vw));
  }

  .popover-right {
    left: 0;
    right: auto;
  }
}

@media (max-width: 480px) {
  .help-circle {
    display: none;
  }

  .date-group {
    flex-direction: column;
  }

  .date-group .search-field {
    width: 100%;
  }

  .date-group .search-field:first-child {
    border-right: 0;
  }

  .search-field {
    min-height: max(60px, 16.6667vw);
  }
}
</style>