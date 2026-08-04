<template>
  <Teleport to="body">
    <Transition name="popup">
      <div
        v-if="show && packageData"
        class="details-overlay"
        @click.self="$emit('close')"
      >
        <article class="details-dialog">
          <button
            type="button"
            class="close-btn"
            aria-label="Close package details"
            @click="$emit('close')"
          >
            <Icon icon="lucide:x" />
          </button>

          <img
            :src="packageData.image"
            :alt="packageData.title"
          />

          <div class="dialog-content">
            <span>{{ packageData.category }}</span>

            <h2>{{ packageData.title }}</h2>

            <p>{{ packageData.longDescription }}</p>

            <ul>
              <li
                v-for="item in packageData.highlights"
                :key="item"
              >
                <Icon icon="lucide:check" />
                {{ item }}
              </li>
            </ul>

            <div class="dialog-footer">
              <strong>${{ packageData.price }}</strong>

              <button
                type="button"
                @click="$emit('book', packageData)"
              >
                Book this package
              </button>
            </div>
          </div>
        </article>
      </div>
    </Transition>
  </Teleport>
</template>

<script setup>
import { Icon } from "@iconify/vue";

defineProps({
  show: {
    type: Boolean,
    default: false
  },

  packageData: {
    type: Object,
    default: null
  }
});

defineEmits(["close", "book"]);
</script>

<style scoped>
* {
  box-sizing: border-box;
  font-family: "Figtree", sans-serif;
}

.details-overlay {
  position: fixed;
  inset: 0;
  z-index: 100000;

  display: grid;
  place-items: center;

  padding: max(20px, 1.3889vw);

  background: rgba(5, 19, 40, 0.66);
}

.details-dialog {
  position: relative;

  width: min(100%, max(760px, 52.7778vw));
  max-height: calc(100vh - max(40px, 2.7778vw));

  overflow: auto;

  border-radius: max(20px, 1.3889vw);

  background: #ffffff;
}

.details-dialog > img {
  width: 100%;
  height: max(280px, 19.4444vw);

  display: block;
  object-fit: cover;
}

.close-btn {
  position: absolute;
  top: max(14px, 0.9722vw);
  right: max(14px, 0.9722vw);
  z-index: 3;

  width: max(40px, 2.7778vw);
  height: max(40px, 2.7778vw);

  display: inline-flex;
  align-items: center;
  justify-content: center;

  padding: 0;

  border: 0;
  border-radius: 50%;

  background: #ffffff;
  color: #123f79;

  font-size: max(18px, 1.25vw);

  cursor: pointer;
}

.dialog-content {
  padding: max(28px, 1.9444vw);
}

.dialog-content > span {
  color: #034acf;

  font-size: max(11px, 0.7639vw);
  font-weight: 700;
}

.dialog-content h2 {
  margin: max(7px, 0.4861vw) 0;
  color: #1A51AD;
  font-weight: 500;
  font-size: max(30px, 2.0833vw);
  margin-bottom: 30px;
}

.dialog-content p {
  color: #657286;
  line-height: 1.5;
  font-size: max(10px, 0.9vw);
  margin-bottom: 30px;
}

.dialog-content ul {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: max(10px, 0.6944vw);

  margin-top: max(20px, 1.3889vw);
  padding: 0;

  list-style: none;
}

.dialog-content li {
  display: flex;
  gap: max(8px, 0.5556vw);

  color: #566273;

  font-size: max(13px, 0.9028vw);
}

.dialog-content li svg {
  flex: 0 0 auto;
  color: #1d9c53;
}

.dialog-footer {
  display: flex;
  align-items: center;
  justify-content: space-between;

  margin-top: max(24px, 1.6667vw);
  padding-top: max(20px, 1.3889vw);

  border-top: max(1px, 0.0694vw) solid #e7ecf2;
}

.dialog-footer strong {
  color: #073C94;

  font-size: max(30px, 2.0833vw);
}

.dialog-footer button {
  min-height: max(46px, 3.1944vw);
  padding: 0 max(22px, 1.5278vw);
  border: 0;
  border-radius: max(10px, 0.6944vw);
  background: #021c44;
  color: #ffffff;
  font-weight: 600;
  cursor: pointer;
  font-size: max(13px, 0.9028vw);
}

.popup-enter-active,
.popup-leave-active {
  transition: opacity 0.2s;
}

.popup-enter-from,
.popup-leave-to {
  opacity: 0;
}

@media (max-width: 768px) {
  .dialog-content ul {
    grid-template-columns: 1fr;
  }
}
</style>