<template>
  <div class="availability-section">
    <div class="section-header">

      <div class="top-row">
        <h2>Check Your Availability</h2>

        <div class="price-match">
          <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
            <path d="M20.59 13.41 12 22l-8.59-8.59A2 2 0 0 1 3 12.83V4a1 1 0 0 1 1-1h8.83a2 2 0 0 1 1.42.59l8.34 8.34a2 2 0 0 1 0 2.83Z"/>
            <circle cx="7.5" cy="7.5" r="1.5"/>
          </svg>
          We Price Match
        </div>
      </div>

      <div v-if="!checkInDate || !checkOutDate" class="notice">
        <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
          <circle cx="12" cy="12" r="10"/>
          <line x1="12" y1="8" x2="12" y2="12"/>
          <line x1="12" y1="16" x2="12.01" y2="16"/>
        </svg>
        Select dates to see this property's availability and prices
      </div>

      <!-- SEARCH BAR -->
      <div class="search-bar">

        <div class="field date-field" ref="dateFieldRef">
          <button class="field-trigger" @click="toggleCalendar">
            <svg class="field-icon" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
              <rect x="3" y="4" width="18" height="18" rx="2"/>
              <line x1="16" y1="2" x2="16" y2="6"/>
              <line x1="8" y1="2" x2="8" y2="6"/>
              <line x1="3" y1="10" x2="21" y2="10"/>
            </svg>
            <span>{{ dateLabel }}</span>
          </button>

          <!-- CALENDAR DROPDOWN -->
          <div v-if="showCalendar" class="calendar-dropdown">
            <div class="calendar-months">
              <div v-for="(month, mIdx) in visibleMonths" :key="mIdx" class="month">

                <div class="month-header">
                  <button
                    v-if="mIdx === 0"
                    class="nav-btn"
                    :class="{ disabled: !canGoPrev }"
                    @click="prevMonth"
                  >‹</button>
                  <span v-else class="nav-spacer"></span>

                  <h4>{{ month.label }}</h4>

                  <button
                    v-if="mIdx === visibleMonths.length - 1"
                    class="nav-btn"
                    @click="nextMonth"
                  >›</button>
                  <span v-else class="nav-spacer"></span>
                </div>

                <div class="weekday-row">
                  <span v-for="d in weekdays" :key="d">{{ d }}</span>
                </div>

                <div class="day-grid">
                  <div
                    v-for="(cell, cIdx) in month.cells"
                    :key="cIdx"
                    class="day-cell"
                    :class="{
                      empty: !cell,
                      past: cell && cell.isPast,
                      selected: cell && isSelected(cell.date),
                      'in-range': cell && isInRange(cell.date),
                      'range-start': cell && isRangeStart(cell.date),
                      'range-end': cell && isRangeEnd(cell.date)
                    }"
                    @click="cell && !cell.isPast && selectDate(cell.date)"
                  >
                    <template v-if="cell">
                      <span class="day-num">{{ cell.day }}</span>
                    </template>
                  </div>
                </div>

              </div>
            </div>
          </div>
        </div>

        <div class="field guest-field" ref="guestFieldRef">
          <button class="field-trigger" @click="toggleGuestSelector">
            <svg class="field-icon" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
              <path d="M20 21v-2a4 4 0 0 0-4-4H8a4 4 0 0 0-4 4v2"/>
              <circle cx="12" cy="7" r="4"/>
            </svg>
            <span>{{ guestLabel }}</span>
          </button>

          <div v-if="showGuestSelector" class="guest-dropdown">

            <div class="guest-row">
              <div class="guest-row-label">
                <strong>Adults</strong>
              </div>
              <div class="stepper">
                <button :disabled="adults <= 1" @click="adults--">−</button>
                <span>{{ adults }}</span>
                <button @click="adults++">+</button>
              </div>
            </div>

            <div class="guest-row">
              <div class="guest-row-label">
                <strong>Children</strong>
              </div>
              <div class="stepper">
                <button :disabled="children <= 0" @click="children--">−</button>
                <span>{{ children }}</span>
                <button @click="children++">+</button>
              </div>
            </div>

            <div class="guest-row">
              <div class="guest-row-label">
                <strong>Rooms</strong>
              </div>
              <div class="stepper">
                <button :disabled="rooms <= 1" @click="rooms--">−</button>
                <span>{{ rooms }}</span>
                <button @click="rooms++">+</button>
              </div>
            </div>

            <button class="done-btn" @click="showGuestSelector = false">Done</button>
          </div>
        </div>

        <button class="search-btn" @click="handleSearch">Search</button>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, computed, onMounted, onBeforeUnmount } from 'vue'

const weekdays = ['Mon', 'Tue', 'Wed', 'Thu', 'Fri', 'Sat', 'Sun']

const roomTypes = [
  { name: 'Standard Double Room with Balcony', beds: '1 queen bed', maxGuests: 2 },
  { name: 'Deluxe Double or Twin Room with Balcony', beds: '2 twin beds', maxGuests: 2 },
  { name: 'Deluxe Double Room with Balcony', beds: '1 king bed', maxGuests: 2 },
  { name: 'Deluxe Triple Room with Balcony', beds: '1 twin bed and 1 king bed', maxGuests: 3 },
  { name: 'King Room with Mountain View', beds: '1 queen bed', maxGuests: 2 },
]

const today = new Date()
today.setHours(0, 0, 0, 0)

const monthCursor = ref(new Date(today.getFullYear(), today.getMonth(), 1))

const checkInDate = ref(null)
const checkOutDate = ref(null)

const showCalendar = ref(false)
const showGuestSelector = ref(false)

const adults = ref(2)
const children = ref(0)
const rooms = ref(1)

const expandedRoom = ref(null)
const revealedPrices = ref({})

const dateFieldRef = ref(null)
const guestFieldRef = ref(null)

function priceForDate(date) {
  const seed = date.getFullYear() * 372 + date.getMonth() * 31 + date.getDate()
  const wiggle = (Math.sin(seed) + 1) / 2 // 0..1
  return Math.round((6300 + wiggle * 10000) / 100) * 100
}

function formatPrice(value) {
  if (!value) return ''
  return (value / 1000).toFixed(1).replace(/\.0$/, '') + 'K'
}

function buildMonth(baseDate) {
  const year = baseDate.getFullYear()
  const month = baseDate.getMonth()
  const firstDay = new Date(year, month, 1)
  const daysInMonth = new Date(year, month + 1, 0).getDate()

  // Monday-first offset
  let leadingBlanks = firstDay.getDay() - 1
  if (leadingBlanks < 0) leadingBlanks = 6

  const cells = []
  for (let i = 0; i < leadingBlanks; i++) cells.push(null)

  for (let d = 1; d <= daysInMonth; d++) {
    const date = new Date(year, month, d)
    cells.push({
      day: d,
      date,
      isPast: date < today,
      price: priceForDate(date),
    })
  }

  return {
    label: baseDate.toLocaleDateString('en-US', { month: 'long', year: 'numeric' }),
    cells,
  }
}

const visibleMonths = computed(() => {
  const first = buildMonth(monthCursor.value)
  const nextBase = new Date(monthCursor.value.getFullYear(), monthCursor.value.getMonth() + 1, 1)
  const second = buildMonth(nextBase)
  return [first, second]
})

const canGoPrev = computed(() => {
  return monthCursor.value.getFullYear() > today.getFullYear() ||
    (monthCursor.value.getFullYear() === today.getFullYear() && monthCursor.value.getMonth() > today.getMonth())
})

function prevMonth() {
  if (!canGoPrev.value) return
  monthCursor.value = new Date(monthCursor.value.getFullYear(), monthCursor.value.getMonth() - 1, 1)
}

function nextMonth() {
  monthCursor.value = new Date(monthCursor.value.getFullYear(), monthCursor.value.getMonth() + 1, 1)
}

/* ---------------- date selection ---------------- */

function sameDay(a, b) {
  return a && b && a.getTime() === b.getTime()
}

function isSelected(date) {
  return sameDay(date, checkInDate.value) || sameDay(date, checkOutDate.value)
}

function isRangeStart(date) {
  return sameDay(date, checkInDate.value)
}

function isRangeEnd(date) {
  return sameDay(date, checkOutDate.value)
}

function isInRange(date) {
  if (!checkInDate.value || !checkOutDate.value) return false
  return date > checkInDate.value && date < checkOutDate.value
}

function selectDate(date) {
  if (!checkInDate.value || (checkInDate.value && checkOutDate.value)) {
    checkInDate.value = date
    checkOutDate.value = null
    return
  }
  if (date <= checkInDate.value) {
    checkInDate.value = date
    checkOutDate.value = null
    return
  }
  checkOutDate.value = date
  showCalendar.value = false
}

const dateLabel = computed(() => {
  const fmt = (d) => d.toLocaleDateString('en-US', { day: '2-digit', month: 'short', year: 'numeric' })
  if (checkInDate.value && checkOutDate.value) {
    return `${fmt(checkInDate.value)} — ${fmt(checkOutDate.value)}`
  }
  if (checkInDate.value) {
    return `${fmt(checkInDate.value)} — Check-out date`
  }
  return 'Check-in date — Check-out date'
})

const guestLabel = computed(() => {
  const adultWord = adults.value === 1 ? 'adult' : 'adults'
  const childWord = children.value === 1 ? 'child' : 'children'
  const roomWord = rooms.value === 1 ? 'room' : 'rooms'
  return `${adults.value} ${adultWord} · ${children.value} ${childWord} · ${rooms.value} ${roomWord}`
})

function toggleCalendar() {
  showCalendar.value = !showCalendar.value
  showGuestSelector.value = false
}

function toggleGuestSelector() {
  showGuestSelector.value = !showGuestSelector.value
  showCalendar.value = false
}

function handleClickOutside(e) {
  if (dateFieldRef.value && !dateFieldRef.value.contains(e.target)) {
    showCalendar.value = false
  }
  if (guestFieldRef.value && !guestFieldRef.value.contains(e.target)) {
    showGuestSelector.value = false
  }
}

onMounted(() => document.addEventListener('click', handleClickOutside))
onBeforeUnmount(() => document.removeEventListener('click', handleClickOutside))

function toggleRoom(name) {
  expandedRoom.value = expandedRoom.value === name ? null : name
}

function handleShowPrices(room) {
  if (!checkInDate.value || !checkOutDate.value) {
    showCalendar.value = true
    return
  }
  revealedPrices.value = {
    ...revealedPrices.value,
    [room.name]: priceForDate(checkInDate.value),
  }
}

function handleSearch() {
}

</script>

<style scoped>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
  font-family: 'Figtree';
}

.availability-section {
  width: 100%;
  background: #f8f9fb;
  margin-bottom: max(50px, 3.4722vw);
}

.section-header {
  max-width: max(1200px, 83.3333vw);
  margin: auto;
  padding: 0 max(20px, 1.3889vw);
}

.top-row {
  display: flex;
  align-items: center;
  justify-content: space-between;
  flex-wrap: wrap;
  gap: max(12px, 0.8333vw);
  margin-bottom: max(18px, 1.25vw);
}

.top-row h2 {
  font-size: max(20px, 1.3889vw);
  font-weight: 550;
  color: #031b35;
}

.price-match {
  display: flex;
  align-items: center;
  gap: max(6px, 0.4167vw);
  font-size: max(13px, 0.9028vw);
  font-weight: 600;
  color: #0179D7;
}

.price-match svg {
  width: max(16px, 1.1111vw);
  height: max(16px, 1.1111vw);
}

.notice {
  display: flex;
  align-items: center;
  gap: max(8px, 0.5556vw);
  color: #b42318;
  background: #fef3f2;
  border: max(1px, 0.0694vw) solid #fee4e2;
  padding: max(10px, 0.6944vw) max(14px, 0.9722vw);
  border-radius: max(8px, 0.5556vw);
  font-size: max(13px, 0.9028vw);
  font-weight: 500;
  margin-bottom: max(18px, 1.25vw);
}

.notice svg {
  width: max(16px, 1.1111vw);
  height: max(16px, 1.1111vw);
  flex-shrink: 0;
}

/* SEARCH BAR */

.search-bar {
  display: grid;
  grid-template-columns: 2fr 1.4fr auto;
  gap: max(12px, 0.8333vw);
  margin-bottom: max(30px, 2.0833vw);
}

.field {
  position: relative;
}

.field-trigger {
  width: 100%;
  display: flex;
  align-items: center;
  gap: max(10px, 0.6944vw);
  background: #fff;
  border: max(1px, 0.0694vw) solid #d1d5db;
  border-radius: max(10px, 0.6944vw);
  padding: max(14px, 0.9722vw) max(16px, 1.1111vw);
  font-size: max(14px, 0.9722vw);
  color: #222;
  cursor: pointer;
  text-align: left;
  transition: .2s;
}

.field-trigger:hover {
  border-color: #0179D7;
}

.field-icon {
  width: max(18px, 1.25vw);
  height: max(18px, 1.25vw);
  color: #0179D7;
  flex-shrink: 0;
}

.search-btn {
  background: #0179D7;
  color: #fff;
  border: none;
  border-radius: max(10px, 0.6944vw);
  padding: max(14px, 0.9722vw) max(32px, 2.2222vw);
  font-size: max(15px, 1.0417vw);
  font-weight: 550;
  cursor: pointer;
  transition: .2s;
}

.search-btn:hover {
  background: #048cf5;
}

/* CALENDAR DROPDOWN */

.calendar-dropdown {
  position: absolute;
  top: calc(100% + max(8px, 0.5556vw));
  left: 0;
  z-index: 30;
  background: #fff;
  border: max(1px, 0.0694vw) solid #e8edf3;
  border-radius: max(16px, 1.1111vw);
  box-shadow: 0 max(15px, 1.0417vw) max(40px, 2.7778vw) rgba(0, 0, 0, .12);
  padding: max(22px, 1.5278vw);
  width: min(max(680px, 47.2222vw), 90vw);
}

.calendar-months {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: max(24px, 1.6667vw);
}

.month-header {
  display: flex;
  align-items: center;
  justify-content: space-between;
  margin-bottom: max(14px, 0.9722vw);
}

.month-header h4 {
  font-size: max(15px, 1.0417vw);
  font-weight: 600;
  color: #222;
}

.nav-btn {
  width: max(26px, 1.8056vw);
  height: max(26px, 1.8056vw);
  border-radius: 50%;
  border: max(1px, 0.0694vw) solid #d1d5db;
  background: #fff;
  color: #374151;
  cursor: pointer;
  font-size: max(14px, 0.9722vw);
  line-height: 1;
}

.nav-btn:hover:not(.disabled) {
  border-color: #0179D7;
  color: #0179D7;
}

.nav-btn.disabled {
  opacity: .3;
  cursor: not-allowed;
}

.nav-spacer {
  width: max(26px, 1.8056vw);
  height: max(26px, 1.8056vw);
}

.weekday-row {
  display: grid;
  grid-template-columns: repeat(7, 1fr);
  margin-bottom: max(6px, 0.4167vw);
}

.weekday-row span {
  text-align: center;
  font-size: max(11px, 0.7639vw);
  font-weight: 600;
  color: #9ca3af;
}

.day-grid {
  display: grid;
  grid-template-columns: repeat(7, 1fr);
  gap: max(2px, 0.1389vw);
}

.day-cell {
  aspect-ratio: 1;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  border-radius: max(8px, 0.5556vw);
  cursor: pointer;
  font-size: max(11px, 0.7639vw);
  color: #222;
  transition: .15s;
}

.day-cell.empty {
  cursor: default;
}

.day-cell:not(.empty):not(.past):hover {
  background: #eef3ff;
}

.day-num {
  font-weight: 600;
  font-size: max(12px, 0.8333vw);
}

.day-price {
  font-size: max(9px, 0.625vw);
  color: #6b7280;
}

.day-cell.past {
  color: #d1d5db;
  cursor: not-allowed;
}

.day-cell.past .day-price {
  color: #e5e7eb;
}

.day-cell.in-range {
  background: #eef3ff;
  border-radius: 0;
}

.day-cell.range-start,
.day-cell.range-end {
  background: #0179D7;
  border-radius: max(8px, 0.5556vw);
}

.day-cell.range-start .day-num,
.day-cell.range-end .day-num,
.day-cell.range-start .day-price,
.day-cell.range-end .day-price {
  color: #fff;
}

/* GUEST DROPDOWN */

.guest-dropdown {
  position: absolute;
  top: calc(100% + max(8px, 0.5556vw));
  right: 0;
  z-index: 30;
  background: #fff;
  border: max(1px, 0.0694vw) solid #e8edf3;
  border-radius: max(16px, 1.1111vw);
  box-shadow: 0 max(15px, 1.0417vw) max(40px, 2.7778vw) rgba(0, 0, 0, .12);
  padding: max(20px, 1.3889vw);
  width: max(260px, 18.0556vw);
}

.guest-row {
  display: flex;
  align-items: center;
  justify-content: space-between;
  padding: max(12px, 0.8333vw) 0;
  border-bottom: max(1px, 0.0694vw) solid #f1f1f1;
}

.guest-row:last-of-type {
  border-bottom: none;
}

.guest-row-label strong {
  font-size: max(14px, 0.9722vw);
  color: #222;
  font-weight: 600;
}

.stepper {
  display: flex;
  align-items: center;
  gap: max(14px, 0.9722vw);
}

.stepper button {
  width: max(28px, 1.9444vw);
  height: max(28px, 1.9444vw);
  border-radius: 50%;
  border: max(1px, 0.0694vw) solid #d1d5db;
  background: #fff;
  color: #0179D7;
  font-size: max(16px, 1.1111vw);
  font-weight: 700;
  cursor: pointer;
  line-height: 1;
}

.stepper button:disabled {
  opacity: .3;
  cursor: not-allowed;
}

.stepper span {
  min-width: max(14px, 0.9722vw);
  text-align: center;
  font-size: max(14px, 0.9722vw);
  font-weight: 600;
}

.done-btn {
  width: 100%;
  margin-top: max(14px, 0.9722vw);
  background: #3B3B3B;
  color: #fff;
  border: none;
  border-radius: max(8px, 0.5556vw);
  padding: max(10px, 0.6944vw);
  font-size: max(13px, 0.9028vw);
  font-weight: 700;
  cursor: pointer;
}

.done-btn:hover {
  background: #3B3B3B;
}

/* ROOM TABLE */

.room-table {
  background: #fff;
  border: max(1px, 0.0694vw) solid #e8edf3;
  border-radius: max(16px, 1.1111vw);
  overflow: hidden;
}

.room-table-header {
  display: grid;
  grid-template-columns: 2fr 1fr max(160px, 11.1111vw);
  background: #1A51AD;
  color: #fff;
  padding: max(14px, 0.9722vw) max(20px, 1.3889vw);
  font-size: max(13px, 0.9028vw);
  font-weight: 600;
}

.room-row {
  display: grid;
  grid-template-columns: 2fr 1fr max(160px, 11.1111vw);
  align-items: center;
  padding: max(18px, 1.25vw) max(20px, 1.3889vw);
  border-bottom: max(1px, 0.0694vw) solid #f1f1f1;
}

.room-row:last-child {
  border-bottom: none;
}

.room-info {
  display: flex;
  align-items: center;
  gap: max(10px, 0.6944vw);
  cursor: pointer;
}

.expand-icon {
  width: max(14px, 0.9722vw);
  height: max(14px, 0.9722vw);
  color: #6b7280;
  transition: transform .2s;
  flex-shrink: 0;
}

.expand-icon.open {
  transform: rotate(90deg);
}

.room-name {
  font-size: max(14px, 0.9722vw);
  font-weight: 600;
  color: #034acf;
  text-decoration: none;
}

.room-name:hover {
  text-decoration: underline;
}

.bed-info {
  display: flex;
  align-items: center;
  gap: max(6px, 0.4167vw);
  margin-top: max(4px, 0.2778vw);
  font-size: max(12px, 0.8333vw);
  color: #6b7280;
}

.bed-info svg {
  width: max(14px, 0.9722vw);
  height: max(14px, 0.9722vw);
}

.guests-col {
  display: flex;
  gap: max(4px, 0.2778vw);
}

.guest-icon svg {
  width: max(16px, 1.1111vw);
  height: max(16px, 1.1111vw);
  color: #374151;
}

.price-col {
  text-align: right;
}

.show-prices-btn {
  background: #034acf;
  color: #fff;
  border: none;
  border-radius: max(6px, 0.4167vw);
  padding: max(10px, 0.6944vw) max(18px, 1.25vw);
  font-size: max(13px, 0.9028vw);
  font-weight: 600;
  cursor: pointer;
  white-space: nowrap;
  transition: .2s;
}

.show-prices-btn:hover {
  background: #0238a1;
}

/* Tablet */

@media (max-width: 992px) {
  .search-bar {
    grid-template-columns: 1fr;
  }

  .calendar-months {
    grid-template-columns: 1fr;
  }

  .calendar-dropdown {
    width: 90vw;
  }
}

/* Mobile */

@media (max-width: 768px) {
  .availability-section {
    padding: 40px 0;
  }

  .section-header {
    padding: 0 15px;
  }

  .top-row h2 {
    font-size: 26px;
  }

  .room-table-header {
    display: none;
  }

  .room-row {
    grid-template-columns: 1fr;
    gap: 12px;
  }

  .guests-col {
    display: none;
  }

  .price-col {
    text-align: left;
  }
}
</style>