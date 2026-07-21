<template>
  <div class="picker">
    <div class="picker-header">
      <button class="nav-btn" @click="prevMonth" aria-label="Previous month">‹</button>
      <div class="month-label">{{ monthLabel }}</div>
      <button class="nav-btn" @click="nextMonth" aria-label="Next month">›</button>
    </div>

    <div class="weekdays">
      <span v-for="d in weekdayLabels" :key="d">{{ d }}</span>
    </div>

    <div class="days">
      <span
        v-for="(cell, i) in cells"
        :key="i"
        class="day"
        :class="{
          empty: !cell,
          disabled: cell && cell.disabled,
          selected: cell && (cell.isCheckIn || cell.isCheckOut),
          'in-range': cell && cell.inRange,
        }"
        @click="cell && !cell.disabled && selectDay(cell.date)"
      >
        {{ cell ? cell.day : "" }}
      </span>
    </div>

    <div class="picker-footer">
      <span class="hint">{{ footerHint }}</span>
      <button class="done-btn" @click="$emit('close')">Done</button>
    </div>
  </div>
</template>

<script setup>
import { ref, computed, watch } from "vue";

const props = defineProps({
  checkIn: { type: Date, default: null },
  checkOut: { type: Date, default: null },
  target: { type: String, default: "checkin" }, // 'checkin' | 'checkout'
});
const emit = defineEmits(["update:checkIn", "update:checkOut", "update:target", "close"]);

const localTarget = ref(props.target);

// The parent can change `target` (e.g. the user clicks the other field)
// without this component unmounting, so keep the local copy in sync.
watch(
  () => props.target,
  (newTarget) => {
    localTarget.value = newTarget;
  }
);

const weekdayLabels = ["Su", "Mo", "Tu", "We", "Th", "Fr", "Sa"];

const today = new Date();
today.setHours(0, 0, 0, 0);

const viewDate = ref(new Date(props.checkIn || today));
viewDate.value.setDate(1);

const monthLabel = computed(() =>
  viewDate.value.toLocaleDateString("en-US", { month: "long", year: "numeric" })
);

function sameDay(a, b) {
  return a && b && a.toDateString() === b.toDateString();
}

const cells = computed(() => {
  const year = viewDate.value.getFullYear();
  const month = viewDate.value.getMonth();
  const firstWeekday = new Date(year, month, 1).getDay();
  const daysInMonth = new Date(year, month + 1, 0).getDate();

  const list = [];
  for (let i = 0; i < firstWeekday; i++) list.push(null);

  for (let day = 1; day <= daysInMonth; day++) {
    const date = new Date(year, month, day);
    const inRange =
      props.checkIn && props.checkOut && date > props.checkIn && date < props.checkOut;
    list.push({
      day,
      date,
      disabled: date < today,
      isCheckIn: sameDay(date, props.checkIn),
      isCheckOut: sameDay(date, props.checkOut),
      inRange,
    });
  }
  return list;
});

const footerHint = computed(() => {
  if (localTarget.value === "checkin") return "Pick a check-in date";
  return "Pick a check-out date";
});

function selectDay(date) {
  if (localTarget.value === "checkin") {
    emit("update:checkIn", date);
    // If the existing check-out is no longer after the new check-in,
    // clear it and move focus to picking a fresh check-out.
    if (!props.checkOut || date >= props.checkOut) {
      emit("update:checkOut", null);
      localTarget.value = "checkout";
      emit("update:target", "checkout");
    }
  } else {
    // Editing check-out. If the clicked date isn't after check-in,
    // treat it as a new check-in instead of an invalid range.
    if (props.checkIn && date <= props.checkIn) {
      emit("update:checkIn", date);
      emit("update:checkOut", null);
      localTarget.value = "checkout";
      emit("update:target", "checkout");
    } else {
      emit("update:checkOut", date);
    }
  }
}

function prevMonth() {
  const d = new Date(viewDate.value);
  d.setMonth(d.getMonth() - 1);
  viewDate.value = d;
}
function nextMonth() {
  const d = new Date(viewDate.value);
  d.setMonth(d.getMonth() + 1);
  viewDate.value = d;
}
</script>

<style scoped>
.picker {
  width: 300px;
  max-height: 380px;
  overflow-y: auto;
  padding: 16px;
  background: #fff;
  border-radius: 10px;
  box-shadow: 0 12px 32px rgba(20, 40, 70, 0.2);
  border: 1px solid #ececec;
}
.picker-header {
  display: flex;
  align-items: center;
  justify-content: space-between;
  margin-bottom: 10px;
}
.month-label {
  font-weight: 700;
  font-size: 14px;
  color: #1a1a1a;
}
.nav-btn {
  background: none;
  border: 1px solid #dcdcdc;
  border-radius: 6px;
  width: 28px;
  height: 28px;
  font-size: 16px;
  line-height: 1;
  cursor: pointer;
  color: #2d6adc;
}
.weekdays {
  display: grid;
  grid-template-columns: repeat(7, 1fr);
  font-size: 11px;
  color: #8a8a8a;
  font-weight: 600;
  text-align: center;
  margin-bottom: 4px;
}
.days {
  display: grid;
  grid-template-columns: repeat(7, 1fr);
  row-gap: 2px;
}
.day {
  display: flex;
  align-items: center;
  justify-content: center;
  height: 32px;
  font-size: 13px;
  border-radius: 6px;
  cursor: pointer;
  color: #1a1a1a;
}
.day:not(.empty):not(.disabled):hover {
  background: #eef2fb;
}
.day.empty {
  cursor: default;
}
.day.disabled {
  color: #d0d0d0;
  cursor: not-allowed;
}
.day.in-range {
  background: #eef2fb;
  border-radius: 0;
}
.day.selected {
  background: #2d6adc;
  color: #fff;
  font-weight: 700;
}
.picker-footer {
  display: flex;
  align-items: center;
  justify-content: space-between;
  margin-top: 12px;
  padding-top: 10px;
  border-top: 1px solid #ececec;
  position: sticky;
  bottom: -16px;
  background: #fff;
  padding-bottom: 16px;
}
.hint {
  font-size: 12px;
  color: #8a8a8a;
}
.done-btn {
  background: #3477d0;
  color: #fff;
  border: none;
  padding: 6px 14px;
  border-radius: 6px;
  font-size: 12.5px;
  font-weight: 700;
  cursor: pointer;
}
</style>
