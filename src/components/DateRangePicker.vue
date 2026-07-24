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
  width: max(300px, 20.8333vw);
  max-height: max(380px, 26.3889vw);
  overflow-y: auto;
  padding: max(16px, 1.1111vw);
  background: #fff;
  border-radius: max(10px, 0.6944vw);
  box-shadow: 0 max(12px, 0.8333vw) max(32px, 2.2222vw) rgba(20, 40, 70, 0.2);
  border: max(1px, 0.0694vw) solid #ececec;
}
.picker-header {
  display: flex;
  align-items: center;
  justify-content: space-between;
  margin-bottom: max(10px, 0.6944vw);
}
.month-label {
  font-weight: 700;
  font-size: max(14px, 0.9722vw);
  color: #1a1a1a;
}
.nav-btn {
  background: none;
  border: max(1px, 0.0694vw) solid #dcdcdc;
  border-radius: max(6px, 0.4167vw);
  width: max(28px, 1.9444vw);
  height: max(28px, 1.9444vw);
  font-size: max(16px, 1.1111vw);
  line-height: 1;
  cursor: pointer;
  color: #2d6adc;
}
.weekdays {
  display: grid;
  grid-template-columns: repeat(7, 1fr);
  font-size: max(11px, 0.7639vw);
  color: #8a8a8a;
  font-weight: 600;
  text-align: center;
  margin-bottom: max(4px, 0.2778vw);
}
.days {
  display: grid;
  grid-template-columns: repeat(7, 1fr);
  row-gap: max(2px, 0.1389vw);
}
.day {
  display: flex;
  align-items: center;
  justify-content: center;
  height: max(32px, 2.2222vw);
  font-size: max(13px, 0.9028vw);
  border-radius: max(6px, 0.4167vw);
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
  margin-top: max(12px, 0.8333vw);
  padding-top: max(10px, 0.6944vw);
  border-top: max(1px, 0.0694vw) solid #ececec;
  position: sticky;
  bottom: min(-16px, -1.1111vw);
  background: #fff;
  padding-bottom: max(16px, 1.1111vw);
}
.hint {
  font-size: max(12px, 0.8333vw);
  color: #8a8a8a;
}
.done-btn {
  background: #3477d0;
  color: #fff;
  border: none;
  padding: max(6px, 0.4167vw) max(14px, 0.9722vw);
  border-radius: max(6px, 0.4167vw);
  font-size: max(12.5px, 0.8681vw);
  font-weight: 700;
  cursor: pointer;
}
</style>
