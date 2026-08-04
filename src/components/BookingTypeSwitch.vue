<template>
  <div
    class="booking-switch"
    :class="{ compact }"
    role="tablist"
    aria-label="Booking type"
  >
    <button
      type="button"
      role="tab"
      class="switch-button"
      :class="{ active: modelValue === 'rooms' }"
      :aria-selected="modelValue === 'rooms'"
      @click="updateValue('rooms')"
    >
      <svg viewBox="0 0 24 24" aria-hidden="true">
        <path d="M4 11h16v7H4z" />
        <path
          d="M6 11V8.8A1.8 1.8 0 0 1 7.8 7h2.4A1.8 1.8 0 0 1 12 8.8V11"
        />
        <path d="M4 18v2M20 18v2" />
      </svg>

      <span>Rooms</span>
    </button>

    <button
      type="button"
      role="tab"
      class="switch-button"
      :class="{ active: modelValue === 'packages' }"
      :aria-selected="modelValue === 'packages'"
      @click="updateValue('packages')"
    >
      <svg viewBox="0 0 24 24" aria-hidden="true">
        <path d="M3.5 12.5 12 4l8.5 8.5-8 8L4 12z" />
        <circle cx="8.5" cy="9" r="1.1" />
      </svg>

      <span>Packages</span>
    </button>
  </div>
</template>

<script setup>
defineProps({
  modelValue: {
    type: String,
    default: "rooms"
  },

  compact: {
    type: Boolean,
    default: false
  }
});

const emit = defineEmits(["update:modelValue"]);

function updateValue(value) {
  emit("update:modelValue", value);
}
</script>

<style scoped>
.booking-switch,
.booking-switch * {
  box-sizing: border-box;
}

.booking-switch {
  width: 100%;
  display: grid;
  grid-template-columns: repeat(2, minmax(0, 1fr));
  gap: max(4px, 0.2778vw);
  padding: max(4px, 0.2778vw);
  border: max(1px, 0.0694vw) solid #dbe4ef;
  border-radius: max(10px, 0.6944vw);
  background: #edf3fa;
}

.switch-button {
  width: 100%;
  min-width: 0;
  min-height: max(38px, 2.6389vw);
  padding: 0 max(12px, 0.8333vw);
  border: 0;
  border-radius: max(7px, 0.4861vw);
  background: transparent;
  color: #68758a;
  font: inherit;
  font-size: max(12px, 0.8333vw);
  font-weight: 700;
  line-height: 1;
  white-space: nowrap;
  cursor: pointer;

  display: inline-flex;
  align-items: center;
  justify-content: center;
  gap: max(6px, 0.4167vw);

  transition:
    background-color 0.18s ease,
    color 0.18s ease,
    box-shadow 0.18s ease,
    transform 0.18s ease;
}

.switch-button:hover:not(.active) {
  background: rgba(255, 255, 255, 0.45);
  color: #315d92;
}

.switch-button:active {
  transform: scale(0.98);
}

.switch-button:focus-visible {
  outline: max(2px, 0.1389vw) solid rgba(1, 121, 215, 0.4);
  outline-offset: max(2px, 0.1389vw);
}

.switch-button svg {
  width: max(15px, 1.0417vw);
  height: max(15px, 1.0417vw);
  flex-shrink: 0;
  fill: none;
  stroke: currentColor;
  stroke-width: 1.7;
  stroke-linecap: round;
  stroke-linejoin: round;
}

.switch-button span {
  display: inline-block;
  min-width: max-content;
  overflow: visible;
  text-overflow: clip;
  white-space: nowrap;
}

.switch-button.active {
  background: #ffffff;
  color: #0b4eac;
  box-shadow:
    0 max(3px, 0.2083vw) max(10px, 0.6944vw)
    rgba(30, 75, 130, 0.1);
}

.booking-switch.compact {
  gap: max(3px, 0.2083vw);
  padding: max(3px, 0.2083vw);
  border-radius: max(8px, 0.5556vw);
}

.booking-switch.compact .switch-button {
  min-height: max(32px, 2.2222vw);
  padding: 0 max(7px, 0.4861vw);
  border-radius: max(6px, 0.4167vw);
  font-size: max(10.5px, 0.7292vw);
  gap: max(5px, 0.3472vw);
}

.booking-switch.compact .switch-button svg {
  width: max(13px, 0.9028vw);
  height: max(13px, 0.9028vw);
}

@media (max-width: 720px) {
  .booking-switch {
    gap: max(4px, 1.1111vw);
    padding: max(4px, 1.1111vw);
    border-radius: max(9px, 2.5vw);
  }

  .switch-button {
    min-height: max(38px, 10.5556vw);
    padding: 0 max(10px, 2.7778vw);
    border-radius: max(7px, 1.9444vw);
    font-size: max(11px, 3.0556vw);
    gap: max(6px, 1.6667vw);
  }

  .switch-button svg {
    width: max(14px, 3.8889vw);
    height: max(14px, 3.8889vw);
  }

  .booking-switch.compact {
    gap: max(3px, 0.8333vw);
    padding: max(3px, 0.8333vw);
    border-radius: max(8px, 2.2222vw);
  }

  .booking-switch.compact .switch-button {
    min-height: max(34px, 9.4444vw);
    padding: 0 max(8px, 2.2222vw);
    border-radius: max(6px, 1.6667vw);
    font-size: max(10.5px, 2.9167vw);
    gap: max(5px, 1.3889vw);
  }

  .booking-switch.compact .switch-button svg {
    width: max(13px, 3.6111vw);
    height: max(13px, 3.6111vw);
  }
}

@media (max-width: 400px) {
  .switch-button {
    padding-inline: max(7px, 1.9444vw);
  }

  .booking-switch.compact .switch-button {
    padding-inline: max(6px, 1.6667vw);
  }
}
</style>