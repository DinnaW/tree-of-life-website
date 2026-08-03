<template>
  <Teleport to="body">
    <Transition name="share-modal">
      <div
        v-if="show"
        class="share-overlay"
        @click="$emit('close')"
      >
        <div
          class="share-modal"
          @click.stop
        >
          <div class="modal-header">
            <div>
              <h2>Share this property</h2>
              <p>Share Tree of Life Nature Resort</p>
            </div>

            <button
              type="button"
              class="close-btn"
              aria-label="Close share popup"
              @click="$emit('close')"
            >
              <i class="fa-solid fa-xmark"></i>
            </button>
          </div>

          <div class="property-preview">
            <img
              :src="image"
              alt="Tree of Life Nature Resort"
            />

            <div class="property-info">
              <span>NATURE RESORT</span>

              <h3>{{ title }}</h3>

              <p>
                <i class="fa-solid fa-star"></i>
                4.8
                <span>·</span>
                Kandy, Sri Lanka
              </p>
            </div>
          </div>

          <div class="modal-body">
            <h4>Share using</h4>

            <div class="share-options">
              <button
                type="button"
                class="share-option"
                @click="shareFacebook"
              >
                <span class="share-icon facebook">
                  <i class="fa-brands fa-facebook-f"></i>
                </span>

                Facebook
              </button>

              <button
                type="button"
                class="share-option"
                @click="shareWhatsApp"
              >
                <span class="share-icon whatsapp">
                  <i class="fa-brands fa-whatsapp"></i>
                </span>

                WhatsApp
              </button>

              <button
                type="button"
                class="share-option"
                @click="shareEmail"
              >
                <span class="share-icon email">
                  <i class="fa-regular fa-envelope"></i>
                </span>

                Email
              </button>

              <button
                type="button"
                class="share-option"
                @click="nativeShare"
              >
                <span class="share-icon more">
                  <i class="fa-solid fa-share-nodes"></i>
                </span>

                More
              </button>
            </div>

            <div class="copy-section">
              <label>Property link</label>

              <div
                class="copy-box"
                :class="{ copied }"
              >
                <i class="fa-solid fa-link"></i>

                <input
                  :value="shareUrl"
                  type="text"
                  readonly
                />

                <button
                  type="button"
                  class="copy-btn"
                  @click="copyLink"
                >
                  <i
                    class="fa-solid"
                    :class="copied ? 'fa-check' : 'fa-copy'"
                  ></i>

                  {{ copied ? "Copied" : "Copy" }}
                </button>
              </div>

              <p
                v-if="copied"
                class="copied-message"
              >
                Link copied successfully
              </p>
            </div>
          </div>
        </div>
      </div>
    </Transition>
  </Teleport>
</template>

<script setup>
import {
  computed,
  onBeforeUnmount,
  ref,
  watch,
} from "vue";

const props = defineProps({
  show: {
    type: Boolean,
    default: false,
  },

  title: {
    type: String,
    default: "Tree of Life Nature Resort",
  },

  image: {
    type: String,
    default: "",
  },

  url: {
    type: String,
    default: "",
  },
});

const emit = defineEmits(["close"]);

const copied = ref(false);
let copiedTimer = null;

const shareUrl = computed(() => {
  return props.url || window.location.href;
});

const shareText = computed(() => {
  return `Take a look at ${props.title} in Kandy, Sri Lanka.`;
});

function openShareWindow(url) {
  window.open(
    url,
    "_blank",
    "noopener,noreferrer,width=720,height=600"
  );
}

function shareFacebook() {
  const url =
    "https://www.facebook.com/sharer/sharer.php?u=" +
    encodeURIComponent(shareUrl.value);

  openShareWindow(url);
}

function shareWhatsApp() {
  const text = `${shareText.value} ${shareUrl.value}`;

  const url =
    "https://wa.me/?text=" +
    encodeURIComponent(text);

  openShareWindow(url);
}

function shareEmail() {
  const subject = encodeURIComponent(
    `Take a look at ${props.title}`
  );

  const body = encodeURIComponent(
    `${shareText.value}\n\n${shareUrl.value}`
  );

  window.location.href =
    `mailto:?subject=${subject}&body=${body}`;
}

async function nativeShare() {
  if (navigator.share) {
    try {
      await navigator.share({
        title: props.title,
        text: shareText.value,
        url: shareUrl.value,
      });
    } catch (error) {
      if (error.name !== "AbortError") {
        copyLink();
      }
    }

    return;
  }

  copyLink();
}

async function copyLink() {
  try {
    await navigator.clipboard.writeText(shareUrl.value);
  } catch {
    const temporaryInput =
      document.createElement("textarea");

    temporaryInput.value = shareUrl.value;
    temporaryInput.style.position = "fixed";
    temporaryInput.style.opacity = "0";

    document.body.appendChild(temporaryInput);
    temporaryInput.select();
    document.execCommand("copy");
    temporaryInput.remove();
  }

  copied.value = true;

  clearTimeout(copiedTimer);

  copiedTimer = setTimeout(() => {
    copied.value = false;
  }, 2500);
}

function handleKeydown(event) {
  if (event.key === "Escape") {
    emit("close");
  }
}

watch(
  () => props.show,
  (isOpen) => {
    if (isOpen) {
      document.body.style.overflow = "hidden";
      window.addEventListener("keydown", handleKeydown);
    } else {
      document.body.style.overflow = "";
      window.removeEventListener("keydown", handleKeydown);
      copied.value = false;
    }
  }
);

onBeforeUnmount(() => {
  document.body.style.overflow = "";
  window.removeEventListener("keydown", handleKeydown);
  clearTimeout(copiedTimer);
});
</script>

<style scoped>
* {
  box-sizing: border-box;
  font-family: "Figtree", sans-serif;
}

/* =========================
   OVERLAY
========================= */

.share-overlay {
  position: fixed;
  inset: 0;
  z-index: 10000;

  display: flex;
  align-items: center;
  justify-content: center;

  padding: max(20px, 1.3889vw);

  background: rgba(15, 25, 40, 0.58);
  backdrop-filter: blur(max(5px, 0.3472vw));
}

/* =========================
   MODAL
========================= */

.share-modal {
  width: min(92%, max(540px, 37.5vw));
  max-height: 90vh;
  overflow-y: auto;

  background: #ffffff;
  border: max(1px, 0.0694vw) solid #e7ebf0;
  border-radius: max(16px, 1.1111vw);

  box-shadow:
    0 max(24px, 1.6667vw)
    max(70px, 4.8611vw)
    rgba(8, 28, 58, 0.24);
}

/* =========================
   HEADER
========================= */

.modal-header {
  display: flex;
  align-items: center;
  justify-content: space-between;
  gap: max(20px, 1.3889vw);

  padding:
    max(21px, 1.4583vw)
    max(24px, 1.6667vw);

  border-bottom:
    max(1px, 0.0694vw)
    solid #edf0f4;
}

.modal-header h2 {
  margin: 0 0 max(4px, 0.2778vw);

  color: #1a51ad;
  font-size: max(20px, 1.3889vw);
  font-weight: 600;
  line-height: 1.25;
}

.modal-header p {
  margin: 0;

  color: #788391;
  font-size: max(12px, 0.8333vw);
  line-height: 1.5;
}

.close-btn {
  width: max(36px, 2.5vw);
  height: max(36px, 2.5vw);
  flex-shrink: 0;

  display: inline-flex;
  align-items: center;
  justify-content: center;

  border: max(1px, 0.0694vw) solid #e2e7ed;
  border-radius: 50%;

  background: #ffffff;
  color: #6f7883;

  font-size: max(13px, 0.9028vw);
  cursor: pointer;

  transition:
    background 0.2s ease,
    border-color 0.2s ease,
    color 0.2s ease;
}

.close-btn:hover {
  border-color: #cbd7e8;
  background: #f3f7fc;
  color: #1a51ad;
}

.close-btn:focus-visible {
  outline: max(3px, 0.2083vw) solid rgba(26, 81, 173, 0.18);
  outline-offset: max(2px, 0.1389vw);
}

/* =========================
   PROPERTY PREVIEW
========================= */

.property-preview {
  display: grid;
  grid-template-columns: max(118px, 8.1944vw) minmax(0, 1fr);
  gap: max(15px, 1.0417vw);

  margin:
    max(22px, 1.5278vw)
    max(24px, 1.6667vw)
    0;

  padding: max(11px, 0.7639vw);

  border: max(1px, 0.0694vw) solid #e5ebf2;
  border-radius: max(12px, 0.8333vw);

  background: #f8fafc;
}

.property-preview img {
  width: 100%;
  height: max(92px, 6.3889vw);

  display: block;
  object-fit: cover;

  border-radius: max(9px, 0.625vw);
}

.property-info {
  min-width: 0;

  display: flex;
  flex-direction: column;
  justify-content: center;
}

.property-info > span {
  margin-bottom: max(5px, 0.3472vw);

  color: #2d6adc;
  font-size: max(10px, 0.6944vw);
  font-weight: 700;
  letter-spacing: max(0.7px, 0.0486vw);
}

.property-info h3 {
  margin: 0 0 max(8px, 0.5556vw);

  overflow: hidden;
  text-overflow: ellipsis;
  white-space: nowrap;

  color: #202833;
  font-size: max(16px, 1.1111vw);
  font-weight: 600;
}

.property-info p {
  margin: 0;

  color: #727d89;
  font-size: max(11px, 0.7639vw);
  line-height: 1.5;
}

.property-info p i {
  margin-right: max(3px, 0.2083vw);
  color: #f1b93c;
}

.property-info p span {
  margin: 0 max(6px, 0.4167vw);
  color: #b3bac3;
}

/* =========================
   BODY
========================= */

.modal-body {
  padding:
    max(23px, 1.5972vw)
    max(24px, 1.6667vw)
    max(25px, 1.7361vw);
}

.modal-body h4 {
  margin: 0 0 max(13px, 0.9028vw);

  color: #303946;
  font-size: max(13px, 0.9028vw);
  font-weight: 600;
}

/* =========================
   SHARE OPTIONS
========================= */

.share-options {
  display: grid;
  grid-template-columns: repeat(4, minmax(0, 1fr));

  gap: max(10px, 0.6944vw);

  margin-bottom: max(24px, 1.6667vw);
}

.share-option {
  min-width: 0;

  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;

  gap: max(8px, 0.5556vw);

  padding:
    max(14px, 0.9722vw)
    max(7px, 0.4861vw);

  border: max(1px, 0.0694vw) solid #e3e8ee;
  border-radius: max(11px, 0.7639vw);

  background: #ffffff;
  color: #3c4652;

  font-size: max(11px, 0.7639vw);
  font-weight: 550;

  cursor: pointer;

  transition:
    background 0.2s ease,
    border-color 0.2s ease,
    color 0.2s ease,
    transform 0.2s ease;
}

.share-option:hover {
  border-color: #b9cbea;
  background: #f6f9fe;
  color: #1a51ad;
  transform: translateY(min(-2px, -0.1389vw));
}

.share-option:active {
  transform: scale(0.98);
}

.share-option:focus-visible {
  outline: max(3px, 0.2083vw) solid rgba(26, 81, 173, 0.17);
  outline-offset: max(2px, 0.1389vw);
}

.share-icon {
  width: max(42px, 2.9167vw);
  height: max(42px, 2.9167vw);

  display: inline-flex;
  align-items: center;
  justify-content: center;

  border-radius: 50%;

  background: #eef4fc;
  color: #1a51ad;

  font-size: max(16px, 1.1111vw);

  transition:
    background 0.2s ease,
    color 0.2s ease;
}

.share-option:hover .share-icon {
  background: #1a51ad;
  color: #ffffff;
}

/* Keep all options visually consistent */

.facebook,
.whatsapp,
.email,
.more {
  background: #eef4fc;
  color: #1a51ad;
}

/* =========================
   COPY LINK
========================= */

.copy-section {
  padding-top: max(20px, 1.3889vw);

  border-top:
    max(1px, 0.0694vw)
    solid #edf0f4;
}

.copy-section label {
  display: block;

  margin-bottom: max(8px, 0.5556vw);

  color: #46515e;
  font-size: max(12px, 0.8333vw);
  font-weight: 600;
}

.copy-box {
  height: max(48px, 3.3333vw);

  display: flex;
  align-items: center;

  overflow: hidden;

  border: max(1px, 0.0694vw) solid #dbe2ea;
  border-radius: max(9px, 0.625vw);

  background: #fafbfc;

  transition:
    border-color 0.2s ease,
    background 0.2s ease,
    box-shadow 0.2s ease;
}

.copy-box:focus-within {
  border-color: #9fb9df;
  background: #ffffff;

  box-shadow:
    0 0 0 max(3px, 0.2083vw)
    rgba(26, 81, 173, 0.1);
}

.copy-box > i {
  flex-shrink: 0;

  margin-left: max(14px, 0.9722vw);

  color: #7792bb;
  font-size: max(12px, 0.8333vw);
}

.copy-box input {
  flex: 1;
  min-width: 0;
  height: 100%;

  padding: 0 max(11px, 0.7639vw);

  border: none;
  outline: none;

  background: transparent;
  color: #64707d;

  font-size: max(11.5px, 0.7986vw);
  text-overflow: ellipsis;
}

.copy-btn {
  height: calc(100% - max(8px, 0.5556vw));
  flex-shrink: 0;

  display: inline-flex;
  align-items: center;
  justify-content: center;

  gap: max(6px, 0.4167vw);

  margin-right: max(4px, 0.2778vw);
  padding: 0 max(15px, 1.0417vw);

  border: none;
  border-radius: max(7px, 0.4861vw);

  background: #1a51ad;
  color: #ffffff;

  font-size: max(11px, 0.7639vw);
  font-weight: 600;

  cursor: pointer;

  transition:
    background 0.2s ease,
    transform 0.2s ease;
}

.copy-btn:hover {
  background: #123f8c;
}

.copy-btn:active {
  transform: scale(0.97);
}

.copy-btn:focus-visible {
  outline: max(3px, 0.2083vw) solid rgba(26, 81, 173, 0.2);
  outline-offset: max(2px, 0.1389vw);
}

.copy-box.copied {
  border-color: #8bc5a0;
  background: #f7fcf8;
}

.copy-box.copied .copy-btn {
  background: #327d50;
}

.copied-message {
  margin: max(9px, 0.625vw) 0 0;

  color: #327d50;
  font-size: max(11px, 0.7639vw);
  font-weight: 550;
}

/* =========================
   TRANSITION
========================= */

.share-modal-enter-active,
.share-modal-leave-active {
  transition: opacity 0.22s ease;
}

.share-modal-enter-active .share-modal,
.share-modal-leave-active .share-modal {
  transition:
    opacity 0.22s ease,
    transform 0.22s ease;
}

.share-modal-enter-from,
.share-modal-leave-to {
  opacity: 0;
}

.share-modal-enter-from .share-modal,
.share-modal-leave-to .share-modal {
  opacity: 0;
  transform: translateY(max(12px, 0.8333vw)) scale(0.98);
}

/* =========================
   MOBILE
========================= */

@media (max-width: 600px) {
  .share-overlay {
    align-items: flex-end;
    padding: 10px;
  }

  .share-modal {
    width: 100%;
    max-height: 92vh;

    border-radius: 16px;
  }

  .modal-header {
    padding: 18px;
  }

  .modal-header h2 {
    font-size: 18px;
  }

  .modal-header p {
    font-size: 12px;
  }

  .property-preview {
    grid-template-columns: 92px minmax(0, 1fr);

    gap: 12px;
    margin: 18px 18px 0;
    padding: 9px;
  }

  .property-preview img {
    height: 82px;
  }

  .property-info h3 {
    font-size: 14px;
  }

  .modal-body {
    padding: 21px 18px 23px;
  }

  .share-options {
    grid-template-columns: repeat(2, minmax(0, 1fr));
    gap: 9px;
  }

  .share-option {
    flex-direction: row;
    justify-content: flex-start;

    padding: 11px 12px;
  }

  .share-icon {
    width: 38px;
    height: 38px;

    font-size: 15px;
  }

  .copy-box {
    height: 47px;
  }

  .copy-btn {
    padding: 0 12px;
  }

  .copy-btn i {
    display: none;
  }
}
</style>