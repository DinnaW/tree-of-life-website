<template>
  <section
    id="rooms"
    class="content-section rooms-packages-section"
  >
    <div class="browse-control">
      <span class="browse-label">BROWSE BY</span>

      <div class="browse-switch">
        <button
          type="button"
          :class="{ active: bookingType === 'rooms' }"
          @click="changeType('rooms')"
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
          :class="{ active: bookingType === 'packages' }"
          @click="changeType('packages')"
        >
          <svg viewBox="0 0 24 24" aria-hidden="true">
            <path d="M3.5 12.5 12 4l8.5 8.5-8 8L4 12z" />
            <circle cx="8.5" cy="9" r="1.1" />
          </svg>

          <span>Packages</span>
        </button>
      </div>
    </div>

    <RoomsSection
      v-if="bookingType === 'rooms'"
      @reserve-room="emit('reserve-room', $event)"
    />

    <PackageResults
      v-else
      @checkout-package="emit('checkout-package', $event)"
    />
  </section>
</template>

<script setup>
import RoomsSection from "./RoomsSection.vue";
import PackageResults from "./PackageResults.vue";

const props = defineProps({
  bookingType: {
    type: String,
    default: "rooms"
  }
});

const emit = defineEmits([
  "update:bookingType",
  "checkout-package",
  "reserve-room"
]);

function changeType(type) {
  if (props.bookingType === type) return;

  emit("update:bookingType", type);
}
</script>

<style scoped>
.rooms-packages-section,
.rooms-packages-section * {
  box-sizing: border-box;
}

.rooms-packages-section {
  position: relative;
  width: 100%;
  scroll-margin-top: max(86px, 5.9722vw);
}

.browse-control {
  position: absolute;
  top: max(49px, 3.4028vw);
  right: max(50px, 3.4722vw);
  z-index: 30;
  width: max(268px, 18.6111vw);
}

.browse-label {
  display: block;
  margin: 0 0 max(8px, 0.5556vw);
  color: #667085;
  font-family: "Figtree", sans-serif;
  font-size: max(10px, 0.6944vw);
  font-weight: 700;
  line-height: 1;
  letter-spacing: max(0.8px, 0.0556vw);
}

.browse-switch {
  display: grid;
  grid-template-columns: repeat(2, minmax(0, 1fr));
  gap: max(4px, 0.2778vw);
  width: 100%;
  padding: max(4px, 0.2778vw);
  border: max(1px, 0.0694vw) solid #d9e3ef;
  border-radius: max(12px, 0.8333vw);
  background: #eaf1f9;
}

.browse-switch button {
  width: 100%;
  min-width: 0;
  min-height: max(42px, 2.9167vw);
  padding: 0 max(16px, 1.1111vw);
  border: 0;
  border-radius: max(9px, 0.625vw);
  background: transparent;
  color: #657286;
  font-family: "Figtree", sans-serif;
  font-size: max(12px, 0.8333vw);
  font-weight: 700;
  line-height: 1;
  white-space: nowrap;
  cursor: pointer;

  display: inline-flex;
  align-items: center;
  justify-content: center;
  gap: max(7px, 0.4861vw);

  transition:
    background-color 0.2s ease,
    color 0.2s ease,
    box-shadow 0.2s ease,
    transform 0.2s ease;
}

.browse-switch button svg {
  width: max(15px, 1.0417vw);
  height: max(15px, 1.0417vw);
  flex: 0 0 auto;
  fill: none;
  stroke: currentColor;
  stroke-width: 1.7;
  stroke-linecap: round;
  stroke-linejoin: round;
}

.browse-switch button span {
  display: inline-block;
  min-width: max-content;
  overflow: visible;
  text-overflow: clip;
  white-space: nowrap;
}

.browse-switch button:hover {
  color: #1a51ad;
  background: rgba(255, 255, 255, 0.4);
}

.browse-switch button:active {
  transform: scale(0.98);
}

.browse-switch button:focus-visible {
  outline: max(2px, 0.1389vw) solid rgba(26, 81, 173, 0.35);
  outline-offset: max(2px, 0.1389vw);
}

.browse-switch button.active {
  background: #ffffff;
  color: #1a51ad;
  box-shadow:
    0 max(4px, 0.2778vw) max(14px, 0.9722vw)
    rgba(29, 72, 126, 0.1);
}

@media (max-width: 1200px) {
  .browse-control {
    right: max(30px, 2.5vw);
  }
}

@media (max-width: 900px) {
  .browse-control {
    position: relative;
    top: auto;
    right: auto;
    width: 100%;
    margin: 0 0 max(24px, 2.6667vw);
  }

  .browse-switch {
    max-width: max(320px, 35.5556vw);
  }
}

@media (max-width: 720px) {
  .rooms-packages-section {
    scroll-margin-top: max(72px, 20vw);
  }

  .browse-control {
    margin-bottom: max(20px, 5.5556vw);
  }

  .browse-label {
    margin-bottom: max(7px, 1.9444vw);
    font-size: max(10px, 2.7778vw);
  }

  .browse-switch {
    width: 100%;
    max-width: none;
    gap: max(4px, 1.1111vw);
    padding: max(4px, 1.1111vw);
    border-radius: max(12px, 3.3333vw);
  }

  .browse-switch button {
    min-height: max(42px, 11.6667vw);
    padding: 0 max(12px, 3.3333vw);
    border-radius: max(9px, 2.5vw);
    font-size: max(12px, 3.3333vw);
    gap: max(7px, 1.9444vw);
  }

  .browse-switch button svg {
    width: max(15px, 4.1667vw);
    height: max(15px, 4.1667vw);
  }
}

@media (max-width: 480px) {
  .browse-switch button {
    min-height: max(40px, 11.1111vw);
    padding: 0 max(10px, 2.7778vw);
    font-size: max(11px, 3.0556vw);
    gap: max(5px, 1.3889vw);
  }

  .browse-switch button svg {
    width: max(14px, 3.8889vw);
    height: max(14px, 3.8889vw);
  }
}
</style>