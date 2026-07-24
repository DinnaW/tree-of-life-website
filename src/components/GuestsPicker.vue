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
  width: max(240px, 16.6667vw);
  padding: max(16px, 1.1111vw);
  background: #fff;
  border-radius: max(10px, 0.6944vw);
  box-shadow: 0 max(12px, 0.8333vw) max(32px, 2.2222vw) rgba(20, 40, 70, 0.2);
  border: max(1px, 0.0694vw) solid #ececec;
}
.row {
  display: flex;
  align-items: center;
  justify-content: space-between;
  padding: max(8px, 0.5556vw) 0;
}
.row + .row {
  border-top: max(1px, 0.0694vw) solid #f0f0f0;
}
.label {
  font-size: max(14px, 0.9722vw);
  font-weight: 600;
  color: #1a1a1a;
}
.stepper {
  display: flex;
  align-items: center;
  gap: max(10px, 0.6944vw);
}
.step-btn {
  width: max(26px, 1.8056vw);
  height: max(26px, 1.8056vw);
  border-radius: 50%;
  border: max(1px, 0.0694vw) solid #dcdcdc;
  background: #fff;
  color: #2d6adc;
  font-size: max(15px, 1.0417vw);
  line-height: 1;
  cursor: pointer;
}
.step-btn:disabled {
  color: #d0d0d0;
  border-color: #ececec;
  cursor: not-allowed;
}
.count {
  min-width: max(16px, 1.1111vw);
  text-align: center;
  font-size: max(14px, 0.9722vw);
  font-weight: 600;
}
.done-btn {
  width: 100%;
  margin-top: max(14px, 0.9722vw);
  background: #3477d0;
  color: #fff;
  border: none;
  padding: max(8px, 0.5556vw) 0;
  border-radius: max(6px, 0.4167vw);
  font-size: max(12.5px, 0.8681vw);
  font-weight: 700;
  cursor: pointer;
}
</style>
