<template>
  <div class="picker">
    <div class="row">
      <span class="label">Rooms</span>
      <div class="stepper">
        <button class="step-btn" :disabled="modelValue <= 1" @click="update(modelValue - 1)">−</button>
        <span class="count">{{ modelValue }}</span>
        <button class="step-btn" @click="update(modelValue + 1)">+</button>
      </div>
    </div>
    <button class="done-btn" @click="$emit('close')">Done</button>
  </div>
</template>

<script setup>
defineProps({
  modelValue: { type: Number, required: true },
});
const emit = defineEmits(["update:modelValue", "close"]);

function update(value) {
  emit("update:modelValue", Math.max(1, value));
}
</script>

<style scoped>
.picker {
  width: 220px;
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
