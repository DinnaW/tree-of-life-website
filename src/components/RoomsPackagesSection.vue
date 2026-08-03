<template>
  <section
    id="rooms"
    class="content-section rooms-packages-section"
  >
    <!-- Always stays in the same top-right position
         for both Rooms and Packages -->
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
            <path d="M6 11V8.8A1.8 1.8 0 0 1 7.8 7h2.4A1.8 1.8 0 0 1 12 8.8V11" />
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
  scroll-margin-top: 86px;
}

/* Fixed placement relative to the Rooms / Packages section */
.browse-control {
  position: absolute;
  top: 49px;
  right: 50px;
  z-index: 30;
  width: 268px;
}

.browse-label {
  display: block;
  margin: 0 0 8px;
  color: #667085;
  font-family: "Figtree", sans-serif;
  font-size: 10px;
  font-weight: 700;
  line-height: 1;
  letter-spacing: 0.08em;
}

.browse-switch {
  display: grid;
  grid-template-columns: repeat(2, minmax(0, 1fr));
  gap: 4px;
  width: 100%;
  padding: 4px;
  border: 1px solid #d9e3ef;
  border-radius: 12px;
  background: #eaf1f9;
}

.browse-switch button {
  display: inline-flex;
  align-items: center;
  justify-content: center;
  gap: 7px;
  min-width: 0;
  min-height: 42px;
  padding: 0 16px;
  border: 0;
  border-radius: 9px;
  background: transparent;
  color: #657286;
  font-family: "Figtree", sans-serif;
  font-size: 12px;
  font-weight: 700;
  line-height: 1;
  cursor: pointer;
  transition:
    background-color 0.2s ease,
    color 0.2s ease,
    box-shadow 0.2s ease;
}

.browse-switch button svg {
  width: 15px;
  height: 15px;
  flex: 0 0 auto;
  fill: none;
  stroke: currentColor;
  stroke-width: 1.7;
  stroke-linecap: round;
  stroke-linejoin: round;
}

.browse-switch button:hover {
  color: #1a51ad;
}

.browse-switch button.active {
  background: #ffffff;
  color: #1a51ad;
  box-shadow: 0 4px 14px rgba(29, 72, 126, 0.1);
}

/* Match the 30px side padding used below 1200px */
@media (max-width: 1200px) {
  .browse-control {
    right: 30px;
  }
}

/* Move switch into normal flow on tablets/mobile */
@media (max-width: 900px) {
  .browse-control {
    position: relative;
    top: auto;
    right: auto;
    width: 100%;
    margin: 0 0 24px;
  }
}

@media (max-width: 480px) {
  .browse-switch button {
    min-height: 40px;
    padding: 0 10px;
    font-size: 11px;
  }
}
</style>
