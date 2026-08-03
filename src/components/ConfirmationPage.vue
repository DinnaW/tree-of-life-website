<template>
  <div class="confirmation-page">
    <div class="container">

      <!-- SUCCESS HEADER -->
      <div class="success-hero">
        <div class="success-icon-wrap">
          <span class="success-icon">
            <i class="fa-solid fa-check"></i>
          </span>
        </div>

        <h2>
          You're all set, {{ guest?.firstName || "guest" }}!
        </h2>

        <p class="subhead">
          Your reservation at Tree of Life Nature Resort is confirmed.
          A confirmation email has been sent to
          <strong>{{ guest?.email || "your email" }}</strong>.
        </p>

        <div class="booking-reference-card">
          <div class="booking-reference-details">
            <span>Booking reference</span>
            <strong>{{ bookingRef }}</strong>
          </div>

          <button
            type="button"
            class="booking-copy-btn"
            @click="copyRef"
          >
            <i
              class="fa-solid"
              :class="copied ? 'fa-check' : 'fa-copy'"
            ></i>

            {{ copied ? "Copied" : "Copy" }}
          </button>
        </div>
      </div>

      <div class="confirmation-body">

        <!-- MAIN: RECEIPT -->
        <div class="confirmation-main">

          <section
            v-if="room"
            class="receipt-card"
          >
            <div class="receipt-room">
              <img
                :src="room.image"
                :alt="room.name"
              />

              <div class="receipt-room-info">
                <h3>{{ room.name }}</h3>

                <p>
                  {{ room.bed || "Single" }} bed ·
                  {{ room.meal || "Bed & Breakfast" }}
                </p>
              </div>

              <div class="receipt-amount">
                <span>Total paid</span>
                <strong>$ {{ formatPrice(total) }}</strong>
              </div>
            </div>

            <dl class="receipt-details">
              <div>
                <dt>Guest</dt>

                <dd>
                  {{ guest?.firstName }}
                  {{ guest?.lastName }}
                </dd>
              </div>

              <div>
                <dt>Duration</dt>

                <dd>
                  {{ nights }}
                  {{ nights === 1 ? "night" : "nights" }}
                  ·
                  {{ room.roomCount || 1 }}
                  {{ (room.roomCount || 1) > 1 ? "rooms" : "room" }}
                </dd>
              </div>

              <div>
                <dt>Check-in</dt>

                <dd>
                  {{ room.checkIn || "From 2:00 PM" }}
                </dd>
              </div>
            </dl>
          </section>

          <!-- WHAT'S NEXT -->
          <section class="next-card">
            <h3>What happens next</h3>

            <ul class="next-list">
              <li>
                <i class="fa-regular fa-envelope"></i>
                Your receipt and confirmation are in your inbox
              </li>

              <li>
                <i class="fa-regular fa-calendar"></i>
                Add your stay to your calendar so you don't forget it
              </li>

              <li>
                <i class="fa-regular fa-comment-dots"></i>
                Need a change? Contact us with your booking reference
              </li>
            </ul>

            <div class="next-actions">
              <button
                type="button"
                class="btn-secondary"
                @click="addToCalendar"
              >
                <i class="fa-regular fa-calendar-plus"></i>
                Add to calendar
              </button>

              <button
                type="button"
                class="btn-secondary"
                @click="printReceipt"
              >
                <i class="fa-solid fa-print"></i>
                Print receipt
              </button>
            </div>
          </section>

        </div>

        <!-- SIDE -->
        <aside class="explore-card">
          <h4>Before your stay</h4>

          <a
            href="#"
            class="explore-link"
            @click.prevent="$emit('go-to-section', 'amenities')"
          >
            Resort amenities
            <i class="fa-solid fa-arrow-right"></i>
          </a>

          <a
            href="#"
            class="explore-link"
            @click.prevent="$emit('go-to-section', 'policies')"
          >
            Check-in & policies
            <i class="fa-solid fa-arrow-right"></i>
          </a>

          <a
            href="#"
            class="explore-link"
            @click.prevent="$emit('go-to-section', 'faq-section')"
          >
            FAQ
            <i class="fa-solid fa-arrow-right"></i>
          </a>

          <p class="support-note">
            Questions? Reach us anytime, quoting
            <strong>{{ bookingRef }}</strong>.
          </p>
        </aside>

      </div>

      <button
        type="button"
        class="back-home-btn"
        @click="$emit('back')"
      >
        Back to home
      </button>

    </div>
  </div>
</template>

<script setup>
import { ref } from "vue";

const props = defineProps({
  room: {
    type: Object,
    default: null,
  },

  nights: {
    type: Number,
    default: 1,
  },

  guest: {
    type: Object,
    default: null,
  },

  total: {
    type: Number,
    default: 0,
  },

  bookingRef: {
    type: String,
    default: "",
  },
});

defineEmits(["back", "go-to-section"]);

const copied = ref(false);

function formatPrice(price) {
  return new Intl.NumberFormat("en-US").format(
    Number(price) || 0
  );
}

async function copyRef() {
  try {
    await navigator.clipboard.writeText(props.bookingRef);

    copied.value = true;

    setTimeout(() => {
      copied.value = false;
    }, 1800);
  } catch (error) {
    console.error("Unable to copy booking reference:", error);
  }
}

function printReceipt() {
  window.print();
}

function addToCalendar() {
  if (!props.room) return;

  const start = new Date();
  start.setDate(start.getDate() + 1);

  const end = new Date(start);
  end.setDate(end.getDate() + props.nights);

  const toICSDate = (date) =>
    date.toISOString().replace(/[-:]/g, "").split(".")[0] + "Z";

  const ics = [
    "BEGIN:VCALENDAR",
    "VERSION:2.0",
    "BEGIN:VEVENT",
    `UID:${props.bookingRef}@treeoflife`,
    `DTSTAMP:${toICSDate(new Date())}`,
    `DTSTART:${toICSDate(start)}`,
    `DTEND:${toICSDate(end)}`,
    `SUMMARY:Stay at Tree of Life Nature Resort — ${props.room.name}`,
    `DESCRIPTION:Booking reference ${props.bookingRef}`,
    "LOCATION:Yahalatenna, Kandy, Sri Lanka",
    "END:VEVENT",
    "END:VCALENDAR",
  ].join("\r\n");

  const blob = new Blob([ics], {
    type: "text/calendar",
  });

  const url = URL.createObjectURL(blob);
  const link = document.createElement("a");

  link.href = url;
  link.download = `tree-of-life-${props.bookingRef}.ics`;

  document.body.appendChild(link);
  link.click();
  document.body.removeChild(link);

  URL.revokeObjectURL(url);
}
</script>

<style scoped>
* {
  box-sizing: border-box;
  font-family: "Figtree", sans-serif;
}

.confirmation-page {
  min-height: 80vh;
  padding: max(60px, 4.1667vw) 0 max(90px, 6.25vw);
  background: #f8f9fb;
}

.container {
  max-width: max(1100px, 76.4vw);
  margin: 0 auto;
  padding: 0 max(20px, 1.3889vw);
}

.success-hero {
  width: 100%;
  max-width: 620px;
  margin: 0 auto max(44px, 3.0556vw);
  text-align: center;
}

.success-icon-wrap {
  display: flex;
  align-items: center;
  justify-content: center;

  width: 78px;
  height: 78px;

  margin: 0 auto 20px;

  border-radius: 50%;
  background: rgba(104, 190, 117, 0.09);
}

.success-icon {
  display: inline-flex;
  align-items: center;
  justify-content: center;

  width: 60px;
  height: 60px;

  border-radius: 50%;
  background: #edf8ee;
  color: #1b7a3d;

  font-size: 22px;
}

.success-hero h2 {
  margin: 0 0 10px;

  color: #1a51ad;

  font-size: max(27px, 1.875vw);
  font-weight: 500;
  line-height: 1.3;
}

.subhead {
  max-width: 600px;
  margin: 0 auto 26px;

  color: #6b7280;

  font-size: 14px;
  line-height: 1.6;
}

.subhead strong {
  color: #2b2f36;
  font-weight: 700;
}

/* BOOKING REFERENCE */

.booking-reference-card {
  display: inline-flex;
  align-items: center;
  justify-content: space-between;

  gap: 24px;

  min-width: 390px;
  padding: 18px 20px;

  border: 1px solid #e0e6ee;
  border-radius: 16px;

  background: #ffffff;
}

.booking-reference-details {
  text-align: left;
}

.booking-reference-details span {
  display: block;

  margin-bottom: 5px;

  color: #9aa3ad;

  font-size: 10.5px;
  font-weight: 700;
  letter-spacing: 0.5px;
  text-transform: uppercase;
}

.booking-reference-details strong {
  display: block;

  color: #1a51ad;

  font-size: 20px;
  font-weight: 700;
  letter-spacing: 0.6px;
  line-height: 1.2;
}

.booking-copy-btn {
  display: inline-flex;
  align-items: center;
  justify-content: center;

  gap: 7px;

  height: 40px;
  padding: 0 16px;

  border: 1px solid #d9e0e8;
  border-radius: 10px;

  background: #ffffff;
  color: #555b64;

  font-size: 12px;
  font-weight: 600;

  cursor: pointer;

  transition:
    border-color 0.15s ease,
    color 0.15s ease,
    background 0.15s ease;
}

.booking-copy-btn i {
  font-size: 12px;
}

.booking-copy-btn:hover {
  border-color: #1a51ad;
  background: #f7faff;
  color: #1a51ad;
}

.confirmation-body {
  display: grid;
  grid-template-columns: minmax(0, 1fr) max(260px, 18.1vw);
  gap: max(24px, 1.6667vw);
  align-items: start;
  margin-bottom: max(32px, 2.2222vw);
}

.receipt-card {
  margin-bottom: max(18px, 1.25vw);
  padding: max(22px, 1.5278vw) max(24px, 1.6667vw);

  border: 1px solid #e8edf3;
  border-radius: max(16px, 1.1111vw);

  background: #ffffff;
}

.receipt-room {
  display: flex;
  align-items: center;

  gap: 14px;

  padding-bottom: max(18px, 1.25vw);
  margin-bottom: max(18px, 1.25vw);

  border-bottom: 1px solid #edf0f4;
}

.receipt-room img {
  width: 60px;
  height: 60px;

  flex-shrink: 0;

  border-radius: 10px;

  object-fit: cover;
}

.receipt-room-info {
  flex: 1;
  min-width: 0;
}

.receipt-room-info h3 {
  margin: 0 0 3px;

  color: #1a1a1a;

  font-size: 16px;
  font-weight: 600;
}

.receipt-room-info p {
  margin: 0;

  color: #7c8693;

  font-size: 12.5px;
}

.receipt-amount {
  flex-shrink: 0;
  text-align: right;
}

.receipt-amount span {
  display: block;

  margin-bottom: 2px;

  color: #9aa3ad;

  font-size: 10.5px;
}

.receipt-amount strong {
  color: #1a51ad;

  font-size: 19px;
  font-weight: 700;
}

.receipt-details {
  display: flex;
  flex-direction: column;

  gap: 10px;

  margin: 0;
}

.receipt-details > div {
  display: flex;
  align-items: baseline;
  justify-content: space-between;

  gap: 12px;
}

.receipt-details dt {
  color: #9aa3ad;
  font-size: 12.5px;
}

.receipt-details dd {
  margin: 0;

  color: #1a1a1a;

  font-size: 13px;
  font-weight: 500;
  text-align: right;
}

.next-card {
  padding: max(22px, 1.5278vw) max(24px, 1.6667vw);
  border: 1px solid #e8edf3;
  border-radius: max(16px, 1.1111vw);
  background: #ffffff;
}

.next-card h3 {
  margin: 0 0 max(14px, 0.9722vw);

  color: #1a1a1a;

  font-size: 15px;
  font-weight: 600;
}

.next-list {
  display: flex;
  flex-direction: column;
  gap: 11px;
  margin: 0 0 max(20px, 1.3889vw);
  padding: 0;
  list-style: none;
}

.next-list li {
  display: flex;
  align-items: center;
  gap: 10px;
  color: #56616e;
  font-size: 13px;
  line-height: 1.5;
}

.next-list i {
  width: 16px;
  flex-shrink: 0;
  color: #1a51ad;
  font-size: 13px;
}

.next-actions {
  display: flex;
  gap: 10px;
}

.btn-secondary {
  display: inline-flex;
  align-items: center;
  justify-content: center;
  flex: 1;
  gap: 8px;
  height: 42px;
  border: 1px solid #dfe3e8;
  border-radius: 10px;
  background: #021c44;
  color: #f6f2f2;
  font-size: 13px;
  font-weight: 600;
  cursor: pointer;
  transition:
    border-color 0.15s ease,
    color 0.15s ease;
}

.btn-secondary:hover {
  border-color: #032d6b;
  color: #ffffff;
}

.explore-card {
  position: sticky;
  top: max(20px, 1.3889vw);
  padding-left: max(4px, 0.2778vw);
}

.explore-card h4 {
  margin: 0 0 max(12px, 0.8333vw);
  color: #9aa3ad;
  font-size: 11px;
  font-weight: 700;
  letter-spacing: 0.6px;
  text-transform: uppercase;
}

.explore-link {
  display: flex;
  align-items: center;
  justify-content: space-between;
  padding: 11px 0;
  border-bottom: 1px solid #edf0f4;
  color: #1a1a1a;
  font-size: 13.5px;
  font-weight: 500;
  text-decoration: none;
  transition: color 0.15s ease;
}

.explore-link i {
  color: #cfd6dd;
  font-size: 11px;
  transition:
    color 0.15s ease,
    transform 0.15s ease;
}

.explore-link:hover {
  color: #1a51ad;
}

.explore-link:hover i {
  color: #1a51ad;
  transform: translateX(2px);
}

.support-note {
  margin: max(14px, 0.9722vw) 0 0;
  color: #9aa3ad;
  font-size: 12px;
  line-height: 1.6;
}

.support-note strong {
  color: #1a1a1a;
}

.back-home-btn {
  display: flex;
  align-items: center;
  justify-content: center;
  height: 46px;
  margin: 0 auto;
  padding: 0 30px;
  border: 1px solid #dfe3e8;
  border-radius: 12px;
  background: #ffffff;
  color: #1a1a1a;
  font-size: 13.5px;
  font-weight: 600;
  cursor: pointer;
  transition:
    border-color 0.15s ease,
    color 0.15s ease;
}

.back-home-btn:hover {
  border-color: #1a51ad;
  color: #1a51ad;
}

@media (max-width: 900px) {
  .confirmation-body {
    grid-template-columns: 1fr;
  }

  .explore-card {
    position: static;
    padding: max(18px, 1.25vw);
    border: 1px solid #e8edf3;
    border-radius: 14px;
    background: #ffffff;
  }
}

@media (max-width: 640px) {
  .success-hero {
    max-width: 100%;
    margin-bottom: 36px;
  }

  .success-icon-wrap {
    width: 72px;
    height: 72px;
    margin-bottom: 18px;
  }

  .success-icon {
    width: 56px;
    height: 56px;
    font-size: 20px;
  }

  .success-hero h2 {
    font-size: 22px;
  }

  .subhead {
    margin-bottom: 22px;
    font-size: 13px;
  }

  .booking-reference-card {
    width: 100%;
    min-width: 0;
    gap: 14px;
    padding: 16px;
  }

  .booking-reference-details strong {
    font-size: 17px;
  }

  .booking-copy-btn {
    height: 38px;
    padding: 0 13px;
  }

  .receipt-room {
    flex-wrap: wrap;
  }

  .receipt-amount {
    width: 100%;
    text-align: left;
  }

  .next-actions {
    flex-direction: column;
  }
}

@media (max-width: 420px) {
  .booking-reference-card {
    flex-direction: column;
  }

  .booking-reference-details {
    text-align: center;
  }

  .booking-copy-btn {
    width: 130px;
  }

  .receipt-details > div {
    align-items: flex-start;
    flex-direction: column;

    gap: 3px;
  }

  .receipt-details dd {
    text-align: left;
  }
}

@media print {
  .next-card,
  .explore-card,
  .back-home-btn,
  .booking-copy-btn {
    display: none;
  }
}
</style>