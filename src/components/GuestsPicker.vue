<template>
  <div class="picker">
    <div class="row">
      <span class="label">Adults</span>
      <div class="stepper">
        <button class="step-btn" :disabled="adults <= 1" @click="$emit('update:adults', Math.max(1, adults - 1))">−</button>
        <span class="count">{{ adults }}</span>
        <button class="step-btn" @click="$emit('update:adults', adults + 1)">+</button>
      </div>
    </div>
    <div class="row">
      <span class="label">Children</span>
      <div class="stepper">
        <button class="step-btn" :disabled="children <= 0" @click="$emit('update:children', Math.max(0, children - 1))">−</button>
        <span class="count">{{ children }}</span>
        <button class="step-btn" @click="$emit('update:children', children + 1)">+</button>
      </div>
    </div>
    <button class="done-btn" @click="$emit('close')">Done</button>
  </div>
</template>

<script setup>
defineProps({
  adults: { type: Number, required: true },
  children: { type: Number, required: true },
});
defineEmits(["update:adults", "update:children", "close"]);
</script>

<style scoped>
.picker {
  width: 240px;
  padding: 16px;
  background: #fff;
  border-radius: 10px;
  box-shadow: 0 12px 32px rgba(20, 40, 70, 0.2);
  border: 1px solid #ececec;
}
.row {
  display: flex;
  align-items: center;
  justify-content: space-between;
  padding: 8px 0;
}
.row + .row {
  border-top: 1px solid #f0f0f0;
}
.label {
  font-size: 14px;
  font-weight: 600;
  color: #1a1a1a;
}
.stepper {
  display: flex;
  align-items: center;
  gap: 10px;
}
.step-btn {
  width: 26px;
  height: 26px;
  border-radius: 50%;
  border: 1px solid #dcdcdc;
  background: #fff;
  color: #2d6adc;
  font-size: 15px;
  line-height: 1;
  cursor: pointer;
}
.step-btn:disabled {
  color: #d0d0d0;
  border-color: #ececec;
  cursor: not-allowed;
}
.count {
  min-width: 16px;
  text-align: center;
  font-size: 14px;
  font-weight: 600;
}
.done-btn {
  width: 100%;
  margin-top: 14px;
  background: #3477d0;
  color: #fff;
  border: none;
  padding: 8px 0;
  border-radius: 6px;
  font-size: 12.5px;
  font-weight: 700;
  cursor: pointer;
}
</style>
