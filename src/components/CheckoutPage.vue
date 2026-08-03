<template>
  <div class="checkout-page">
    <div class="container">

      <!-- BREADCRUMB -->
      <div class="checkout-breadcrumb">
        <a href="#" @click.prevent="$emit('back')">
          <i class="fa-solid fa-arrow-left"></i>
          Home
        </a>
        <span class="crumb-sep">/</span>
        <strong>Check Out</strong>
      </div>

      <!-- HEAD -->
      <header class="checkout-head">
        <h2>Complete your reservation</h2>
        <p class="subhead" v-if="room">
          You're booking the <strong>{{ room.name }}</strong> — just a few details and you're set.
        </p>
        <p class="subhead" v-else>
          Add a room from the Rooms section to start your reservation.
        </p>
      </header>

      <!-- STEP TRACK -->
      <div class="step-track" role="list" aria-label="Checkout steps">
        <div class="step-node" :class="{ active: step === 1, done: step > 1 }">
          <span class="step-circle">
            <i v-if="step > 1" class="fa-solid fa-check"></i>
            <span v-else>1</span>
          </span>
          <span class="step-label">Guest details</span>
        </div>

        <div class="step-line" :class="{ filled: step > 1 }"></div>

        <div class="step-node" :class="{ active: step === 2 }">
          <span class="step-circle">2</span>
          <span class="step-label">Payment</span>
        </div>
      </div>

      <!-- BODY -->
      <div class="checkout-body">

        <!-- MAIN COLUMN -->
        <div class="checkout-main">

          <!-- STEP 1 -->
          <form v-if="step === 1" @submit.prevent="goToPayment" novalidate>

            <section class="form-card">
              <div class="form-card-head">
                <span class="fc-icon"><i class="fa-solid fa-user"></i></span>
                <div>
                  <h3>Guest details</h3>
                  <p>Who's checking in</p>
                </div>
              </div>

              <div class="field-row">
                <div class="field">
                  <label>First name</label>
                  <input v-model="form.firstName" type="text" placeholder="Enter your first name" required />
                </div>
                <div class="field">
                  <label>Last name</label>
                  <input v-model="form.lastName" type="text" placeholder="Enter your last name" required />
                </div>
              </div>
            </section>

            <section class="form-card">
              <div class="form-card-head">
                <span class="fc-icon"><i class="fa-solid fa-envelope"></i></span>
                <div>
                  <h3>Contact information</h3>
                  <p>Where we'll send your confirmation</p>
                </div>
              </div>

              <div class="field-row">
                <div class="field">
                  <label>Email</label>
                  <input v-model="form.email" type="email" placeholder="johndoe@gmail.com" required />
                </div>
                <div class="field">
                  <label>Confirm email</label>
                  <input v-model="form.confirmEmail" type="email" placeholder="johndoe@gmail.com" required />
                  <span v-if="form.confirmEmail && form.confirmEmail !== form.email" class="field-error">
                    <i class="fa-solid fa-circle-exclamation"></i>
                    Emails don't match
                  </span>
                </div>
              </div>

              <div class="field-row">
                <div class="field">
                  <label>Phone number</label>
                  <input v-model="form.phone" type="tel" placeholder="+94 7X XXX XXXX" required />
                  <span v-if="phoneError" class="field-error">
                    <i class="fa-solid fa-circle-exclamation"></i>
                    Please enter a valid phone number
                  </span>
                </div>
                <div class="field">
                  <label>NIC / Passport no.</label>
                  <input v-model="form.nic" type="text" placeholder="Enter your NIC / passport number" />
                </div>
              </div>
            </section>

            <section class="form-card">
              <div class="form-card-head">
                <span class="fc-icon"><i class="fa-solid fa-location-dot"></i></span>
                <div>
                  <h3>Address</h3>
                  <p>For your booking record</p>
                </div>
              </div>

              <div class="field">
                <label>Street address</label>
                <textarea v-model="form.address" rows="2" placeholder="207/12 Main street"></textarea>
              </div>

              <div class="field-row">
                <div class="field">
                  <label>Country</label>
                  <select v-model="form.country">
                    <option value="">Select your country</option>
                    <option>Sri Lanka</option>
                    <option>India</option>
                    <option>United Kingdom</option>
                    <option>United States</option>
                  </select>
                </div>
                <div class="field">
                  <label>City</label>
                  <input v-model="form.city" type="text" placeholder="Enter your city" />
                </div>
              </div>
            </section>

            <section class="form-card">
              <div class="form-card-head">
                <span class="fc-icon"><i class="fa-solid fa-suitcase-rolling"></i></span>
                <div>
                  <h3>Stay details</h3>
                  <p>Help us prepare for your arrival</p>
                </div>
              </div>

              <div class="field-row">
                <div class="field">
                  <label>Estimated arrival time</label>
                  <input v-model="form.arrivalTime" type="time" />
                </div>
              </div>

              <div class="field">
                <label>Special requests <span class="field-optional">(optional)</span></label>
                <textarea v-model="form.notes" rows="3" placeholder="Late check-in, dietary needs, celebration, accessibility..."></textarea>
              </div>
            </section>

            <p v-if="!step1Valid && step1MissingFields.length" class="payment-missing">
              <i class="fa-solid fa-circle-info"></i>
              Still needed: {{ step1MissingFields.join(", ") }}
            </p>

            <button type="submit" class="checkout-submit" :disabled="!room || !step1Valid">
              Continue to payment
              <i class="fa-solid fa-arrow-right"></i>
            </button>
          </form>

          <!-- STEP 2: PAYMENT -->
          <form v-else class="payment-form" @submit.prevent="confirmReservation" novalidate>

            <section class="form-card">
              <div class="form-card-head">
                <span class="fc-icon"><i class="fa-solid fa-credit-card"></i></span>
                <div>
                  <h3>Payment details</h3>
                  <p>Secured and encrypted</p>
                </div>
              </div>

              <!-- CARD BRANDS ACCEPTED -->
              <div class="card-brands">
                <span class="brand-label">We accept</span>
                <i class="fa-brands fa-cc-visa" :class="{ dim: cardBrand && cardBrand !== 'visa' }"></i>
                <i class="fa-brands fa-cc-mastercard" :class="{ dim: cardBrand && cardBrand !== 'mastercard' }"></i>
                <i class="fa-brands fa-cc-amex" :class="{ dim: cardBrand && cardBrand !== 'amex' }"></i>
              </div>

              <div class="field">
                <label>Cardholder name</label>
                <input
                  v-model="payment.cardName"
                  type="text"
                  placeholder="Name as shown on card"
                  autocomplete="cc-name"
                  required
                />
              </div>

              <div class="field">
                <label>Card number</label>
                <div class="card-number-wrap">
                  <input
                    :value="payment.cardNumber"
                    @input="onCardNumberInput"
                    type="text"
                    inputmode="numeric"
                    placeholder="1234 5678 9012 3456"
                    maxlength="19"
                    autocomplete="cc-number"
                    required
                  />
                  <i
                    class="fa-brands card-icon"
                    :class="cardIconClass"
                  ></i>
                </div>
              </div>

              <div class="field-row payment-row-3">
                <div class="field">
                  <label>Expiry date</label>
                  <input
                    :value="payment.expiry"
                    @input="onExpiryInput"
                    type="text"
                    inputmode="numeric"
                    placeholder="MM/YY"
                    maxlength="5"
                    autocomplete="cc-exp"
                    required
                  />
                </div>
                <div class="field">
                  <label>CVV</label>
                  <input
                    v-model="payment.cvv"
                    type="password"
                    inputmode="numeric"
                    placeholder="•••"
                    maxlength="4"
                    autocomplete="cc-csc"
                    required
                  />
                </div>
              </div>

              <label class="checkbox-row">
                <input type="checkbox" v-model="payment.saveCard" />
                <span>Save this card for faster checkout next time</span>
              </label>

              <label class="checkbox-row">
                <input type="checkbox" v-model="payment.agreeTerms" required />
                <span>
                  I agree to the
                  <a href="#" @click.prevent>cancellation policy</a>
                  and
                  <a href="#" @click.prevent>terms of service</a>
                </span>
              </label>
            </section>

            <p v-if="!paymentValid && missingFields.length" class="payment-missing">
                <i class="fa-solid fa-circle-info"></i>
                Still needed: {{ missingFields.join(", ") }}
            </p>
            
            <div class="payment-actions">
              <button type="button" class="checkout-back" @click="step = 1">
                <i class="fa-solid fa-arrow-left"></i>
                Back
              </button>
              <button type="submit" class="checkout-submit" :disabled="!paymentValid">
                <template v-if="!isProcessing">
                  <i class="fa-solid fa-lock"></i>
                  Pay $ {{ formatPrice(total) }} now
                </template>
                <template v-else>
                  <i class="fa-solid fa-circle-notch fa-spin"></i>
                  Processing...
                </template>
              </button>
            </div>

            <p class="payment-fineprint">
              <i class="fa-solid fa-shield-halved"></i>
              Your payment is protected with 256-bit SSL encryption. You won't be charged until your reservation is confirmed.
            </p>
          </form>

        </div>

        <!-- SUMMARY -->
        <aside class="summary-card">
          <div v-if="!room" class="summary-empty">
            <span class="summary-empty-icon"><i class="fa-solid fa-cart-shopping"></i></span>
            <h4>Your cart is empty</h4>
            <p>Pick a room and it'll show up here.</p>
            <button type="button" class="summary-empty-cta" @click="$emit('go-to-section', 'rooms')">
              Browse rooms
            </button>
          </div>

          <template v-else>
            <div class="summary-room">
              <button
                type="button"
                class="summary-remove"
                aria-label="Remove room from cart"
                @click="$emit('remove')"
              >
                <i class="fa-solid fa-trash-can"></i>
              </button>

              <img :src="room.image" :alt="room.name" />
              <div class="summary-room-info">
                <span v-if="room.tag" class="summary-tag">{{ room.tag }}</span>
                <h4>{{ room.name }}</h4>
                <p>{{ room.bed || "Single" }} bed · {{ room.meal || "Bed & Breakfast" }}</p>
              </div>
            </div>

            <div class="summary-meta">
              <div class="summary-meta-row">
                <span><i class="fa-regular fa-calendar"></i> Duration</span>
                <strong>{{ nights }} {{ nights === 1 ? "night" : "nights" }}</strong>
              </div>
              <div class="summary-meta-row">
                <span><i class="fa-solid fa-door-open"></i> Rooms</span>
                <strong>{{ room.roomCount || 1 }}</strong>
              </div>
              <div class="summary-meta-row" v-if="room.extraBeds">
                <span><i class="fa-solid fa-bed"></i> Extra beds</span>
                <strong>{{ room.extraBeds }}</strong>
              </div>
            </div>

            <div class="summary-breakdown">
              <div class="summary-row">
                <span>$ {{ formatPrice(room.price) }} × {{ nights }} {{ nights === 1 ? "night" : "nights" }} × {{ room.roomCount || 1 }}</span>
                <span>$ {{ formatPrice(room.price * (room.roomCount || 1) * nights) }}</span>
              </div>
              <div class="summary-row" v-if="room.extraBeds">
                <span>Extra beds ({{ room.extraBeds }} × $15)</span>
                <span>$ {{ formatPrice(room.extraBeds * 15) }}</span>
              </div>
              <div class="summary-row summary-total">
                <span>Total</span>
                <span>$ {{ formatPrice(total) }}</span>
              </div>
            </div>

            <ul class="summary-trust">
              <li><i class="fa-solid fa-circle-check"></i> {{ room.cancellation || "Free cancellation up to 48 hours before check-in" }}</li>
              <li><i class="fa-solid fa-circle-check"></i> No booking fees</li>
              <li><i class="fa-solid fa-circle-check"></i> Free parking on site</li>
            </ul>

            <div class="summary-secure">
              <i class="fa-solid fa-lock"></i>
              Secure checkout · your details are encrypted
            </div>
          </template>
        </aside>

      </div>
    </div>
  </div>
</template>

<script setup>
import { reactive, ref, computed, watch } from "vue";

const props = defineProps({
  room: { type: Object, default: null },
  nights: { type: Number, default: 1 },
});

const emit = defineEmits(["back", "remove", "confirmed", "go-to-section"]);

const step = ref(1);

const form = reactive({
  firstName: "",
  lastName: "",
  email: "",
  confirmEmail: "",
  address: "",
  country: "",
  city: "",
  nic: "",
  phone: "",
  arrivalTime: "",
  notes: "",
});

const phoneError = ref(false);

const total = computed(() => {
  if (!props.room) return 0;
  return (
    props.room.price * (props.room.roomCount || 1) * props.nights +
    (props.room.extraBeds || 0) * 15
  );
});

watch(
  () => props.room,
  () => {
    step.value = 1;
  }
);

function goToPayment() {
  phoneError.value = form.phone.trim().length < 7;
  if (!step1Valid.value) return;
  step.value = 2;
  window.scrollTo({ top: 0, behavior: "smooth" });
}

const step1MissingFields = computed(() => {
  const missing = [];
  if (form.firstName.trim().length < 1) missing.push("first name");
  if (form.lastName.trim().length < 1) missing.push("last name");
  if (!/^\S+@\S+\.\S+$/.test(form.email)) missing.push("valid email");
  if (form.confirmEmail.trim() !== form.email.trim() || !form.confirmEmail) missing.push("matching confirm email");
  if (form.phone.trim().length < 7) missing.push("valid phone number");
  return missing;
});

const step1Valid = computed(() => step1MissingFields.value.length === 0);

function formatPrice(price) {
  return new Intl.NumberFormat("en-US").format(Number(price) || 0);
}

const payment = reactive({
  cardName: "",
  cardNumber: "",
  expiry: "",
  cvv: "",
  saveCard: false,
  agreeTerms: false,
});

const isProcessing = ref(false);

const cardBrand = computed(() => {
  const digits = payment.cardNumber.replace(/\s/g, "");
  if (/^4/.test(digits)) return "visa";
  if (/^(5[1-5]|2[2-7])/.test(digits)) return "mastercard";
  if (/^3[47]/.test(digits)) return "amex";
  return "";
});

const cardIconClass = computed(() => {
  if (cardBrand.value === "visa") return "fa-cc-visa";
  if (cardBrand.value === "mastercard") return "fa-cc-mastercard";
  if (cardBrand.value === "amex") return "fa-cc-amex";
  return "fa-credit-card-blank"; 
});

function onCardNumberInput(e) {
  const digits = e.target.value.replace(/\D/g, "").slice(0, 16);
  payment.cardNumber = digits.replace(/(.{4})/g, "$1 ").trim();
}

function onExpiryInput(e) {
  let digits = e.target.value.replace(/\D/g, "").slice(0, 4);
  if (digits.length >= 3) {
    digits = `${digits.slice(0, 2)}/${digits.slice(2)}`;
  }
  payment.expiry = digits;
}

const paymentValid = computed(() => missingFields.value.length === 0);

const missingFields = computed(() => {
  const digits = payment.cardNumber.replace(/\s/g, "");
  const missing = [];

  if (payment.cardName.trim().length <= 1) missing.push("cardholder name");
  if (digits.length < 15) missing.push("card number");
  if (!/^\d{2}\/\d{2}$/.test(payment.expiry)) missing.push("expiry date");
  if (payment.cvv.length < 3) missing.push("CVV");
  if (!payment.agreeTerms) missing.push("agree to terms");

  return missing;
});

function confirmReservation() {
  console.log("[Checkout] confirmReservation called. paymentValid:", paymentValid.value, "isProcessing:", isProcessing.value, "missingFields:", missingFields.value);

  if (!paymentValid.value || isProcessing.value) {
    console.log("[Checkout] Blocked — not proceeding.");
    return;
  }

  isProcessing.value = true;
  console.log("[Checkout] Processing started...");

  setTimeout(() => {
    isProcessing.value = false;
    console.log("[Checkout] Emitting 'confirmed' event with:", {
      room: props.room,
      guest: { ...form },
      total: total.value,
    });
    emit("confirmed", { room: props.room, guest: { ...form }, total: total.value });
  }, 1200);
}
</script>

<style scoped>
* {
  box-sizing: border-box;
  font-family: "Figtree", sans-serif;
}

.checkout-page {
  background: #f8f9fb;
  padding: max(50px, 3.4722vw) 0 max(90px, 6.25vw);
  min-height: 70vh;
}

.container {
  max-width: max(1200px, 83.3333vw);
  margin: 0 auto;
  padding: 0 max(20px, 1.3889vw);
}

/* BREADCRUMB */

.checkout-breadcrumb {
  display: flex;
  align-items: center;
  gap: 10px;
  margin-bottom: max(36px, 2.5vw);
  font-size: 13px;
  color: #6b7280;
}
.checkout-breadcrumb a {
  display: inline-flex;
  align-items: center;
  gap: 7px;
  color: #6b7280;
  text-decoration: none;
  font-weight: 500;
  transition: color 0.15s ease;
}
.checkout-breadcrumb a:hover {
  color: #1a51ad;
}
.checkout-breadcrumb a i {
  font-size: 11px;
}
.crumb-sep {
  color: #ccc;
}
.checkout-breadcrumb strong {
  color: #1a1a1a;
}

/* HEAD */

.checkout-head {
  margin-bottom: max(36px, 2.5vw);
}

.checkout-head h2 {
  font-size: max(34px, 2.3611vw);
  font-weight: 500;
  color: #1a51ad;
  margin: 0 0 max(12px, 0.8333vw);
  line-height: 1.3;
}

.subhead {
  margin: 0;
  font-size: max(14.5px, 1.0069vw);
  color: #6b7280;
}
.subhead strong {
  color: #2b2f36;
  font-weight: 600;
}

/* STEP TRACK */

.step-track {
  display: flex;
  align-items: center;
  gap: 0;
  margin-bottom: max(40px, 2.7778vw);
  max-width: 420px;
}

.step-node {
  display: flex;
  align-items: center;
  gap: 10px;
  flex-shrink: 0;
}

.step-circle {
  width: 34px;
  height: 34px;
  border-radius: 50%;
  background: #eef1ec;
  color: #9aa3ad;
  display: inline-flex;
  align-items: center;
  justify-content: center;
  font-size: 13px;
  font-weight: 700;
  border: 2px solid #e8edf3;
  transition: all 0.2s ease;
  flex-shrink: 0;
}

.step-node.active .step-circle {
  background: #1a51ad;
  border-color: #1a51ad;
  color: #fff;
}

.step-node.done .step-circle {
  background: #1b7a3d;
  border-color: #1b7a3d;
  color: #fff;
}

.step-label {
  font-size: 13.5px;
  font-weight: 600;
  color: #9aa3ad;
  white-space: nowrap;
}

.step-node.active .step-label,
.step-node.done .step-label {
  color: #1a1a1a;
}

.step-line {
  flex: 1;
  height: 2px;
  min-width: 40px;
  background: #e8edf3;
  margin: 0 14px;
  border-radius: 2px;
  position: relative;
}
.step-line.filled {
  background: #1b7a3d;
}

/* BODY LAYOUT */

.checkout-body {
  display: grid;
  grid-template-columns: minmax(0, 1fr) max(340px, 23.6vw);
  gap: max(28px, 1.9444vw);
  align-items: start;
}

/* FORM CARDS */

.form-card {
  background: #ffffff;
  border: 1px solid #e8edf3;
  border-radius: max(16px, 1.1111vw);
  padding: max(26px, 1.8056vw) max(28px, 1.9444vw);
  margin-bottom: max(20px, 1.3889vw);
}

.form-card-head {
  display: flex;
  align-items: center;
  gap: 14px;
  margin-bottom: max(22px, 1.5278vw);
}

.fc-icon {
  width: 42px;
  height: 42px;
  border-radius: 12px;
  background: #edf3fb;
  color: #1a51ad;
  display: inline-flex;
  align-items: center;
  justify-content: center;
  font-size: 16px;
  flex-shrink: 0;
}

.form-card-head h3 {
  margin: 0;
  font-size: max(17px, 1.1806vw);
  font-weight: 600;
  color: #1a1a1a;
}

.form-card-head p {
  margin: 2px 0 0;
  font-size: 12.5px;
  color: #9aa3ad;
}

.field-row {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 18px;
  margin-bottom: 18px;
}
.field-row:last-child {
  margin-bottom: 0;
}

.payment-row-3 {
  grid-template-columns: 1fr 1fr;
}

.field {
  display: flex;
  flex-direction: column;
  gap: 7px;
  margin-bottom: 18px;
}
.field:last-child {
  margin-bottom: 0;
}

.field label {
  font-size: 12.5px;
  font-weight: 600;
  color: #444;
  letter-spacing: 0.2px;
}

.field-optional {
  font-weight: 400;
  color: #9aa3ad;
}

.field input,
.field select,
.field textarea {
  border: 1px solid #dfe3e8;
  border-radius: 10px;
  padding: 12px 14px;
  font-size: 14px;
  font-family: inherit;
  background: #fafbfc;
  resize: vertical;
  color: #1a1a1a;
  transition: border-color 0.15s ease, background 0.15s ease;
  width: 100%;
}

.field input::placeholder,
.field textarea::placeholder {
  color: #adb5bd;
}

.field input:focus,
.field select:focus,
.field textarea:focus {
  outline: none;
  border-color: #1a51ad;
  background: #fff;
}

.field-error {
  display: flex;
  align-items: center;
  gap: 6px;
  color: #e53935;
  font-size: 12px;
  font-weight: 500;
}

/* PAYMENT SPECIFIC */

.card-brands {
  display: flex;
  align-items: center;
  gap: 10px;
  margin-bottom: max(20px, 1.3889vw);
}
.brand-label {
  font-size: 12px;
  color: #9aa3ad;
  margin-right: 4px;
}
.card-brands i {
  font-size: 24px;
  color: #444;
  transition: opacity 0.15s ease;
}
.card-brands i.dim {
  opacity: 0.25;
}

.card-number-wrap {
  position: relative;
}
.card-number-wrap input {
  padding-right: 44px;
  letter-spacing: 1px;
}
.card-icon {
  position: absolute;
  right: 14px;
  top: 50%;
  transform: translateY(-50%);
  font-size: 22px;
  color: #1a51ad;
  pointer-events: none;
}
.fa-credit-card-blank::before {
  content: "\f09d"; /* fa-credit-card fallback */
  font-family: "Font Awesome 6 Free";
  font-weight: 900;
  color: #cfd6dd;
}

.checkbox-row {
  display: flex;
  align-items: flex-start;
  gap: 10px;
  margin-top: 16px;
  font-size: 12.5px;
  color: #6b7280;
  cursor: pointer;
  line-height: 1.5;
}
.checkbox-row input {
  margin-top: 2px;
  width: 15px;
  height: 15px;
  accent-color: #1a51ad;
  cursor: pointer;
  flex-shrink: 0;
}
.checkbox-row a {
  color: #1a51ad;
  font-weight: 600;
  text-decoration: none;
}
.checkbox-row a:hover {
  text-decoration: underline;
}

.payment-missing {
  display: flex;
  align-items: center;
  gap: 8px;
  margin: 0 0 max(20px, 1.3889vw);
  padding: 10px 14px;
  background: #fff8ec;
  border: 1px solid #f4e2b8;
  border-radius: 10px;
  font-size: 12.5px;
  color: #8a6d1a;
}
.payment-missing i {
  color: #c99a1f;
  flex-shrink: 0;
}

.payment-fineprint {
  display: flex;
  align-items: flex-start;
  gap: 8px;
  margin: max(14px, 0.9722vw) 0 0;
  font-size: 12px;
  color: #9aa3ad;
  line-height: 1.6;
}
.payment-fineprint i {
  margin-top: 2px;
  color: #1b7a3d;
  flex-shrink: 0;
}

/* SUBMIT / PAYMENT ACTIONS */

.checkout-submit {
  width: 100%;
  height: 52px;
  border: none;
  border-radius: max(14px, 0.9722vw);
  background: #021c44;
  color: #fff;
  font-family: inherit;
  font-size: 15px;
  font-weight: 600;
  display: inline-flex;
  align-items: center;
  justify-content: center;
  gap: 10px;
  cursor: pointer;
  transition: background 0.15s ease, transform 0.15s ease;
}
.checkout-submit:hover:not(:disabled) {
  background: #032d6b;
  transform: translateY(-1px);
}
.checkout-submit:disabled {
  opacity: 0.45;
  cursor: not-allowed;
}

.payment-actions {
  display: flex;
  gap: 14px;
}

.checkout-back {
  flex-shrink: 0;
  border: 1px solid #dfe3e8;
  background: #fff;
  border-radius: 14px;
  padding: 0 22px;
  height: 52px;
  display: inline-flex;
  align-items: center;
  gap: 8px;
  cursor: pointer;
  font-size: 14px;
  font-weight: 600;
  color: #444;
  transition: border-color 0.15s ease, color 0.15s ease;
}
.checkout-back:hover {
  border-color: #1a51ad;
  color: #1a51ad;
}

/* SUMMARY CARD */

.summary-card {
  position: sticky;
  top: max(20px, 1.3889vw);
  background: #ffffff;
  border: 1px solid #e8edf3;
  border-radius: max(18px, 1.25vw);
  padding: max(24px, 1.6667vw);
  box-shadow: 0 12px 35px rgba(28, 55, 89, 0.06);
}

.summary-empty {
  text-align: center;
  padding: max(20px, 1.3889vw) max(10px, 0.6944vw) max(6px, 0.4167vw);
}
.summary-empty-icon {
  width: 52px;
  height: 52px;
  margin: 0 auto 14px;
  border-radius: 50%;
  background: #f3f6ff;
  color: #1a51ad;
  display: inline-flex;
  align-items: center;
  justify-content: center;
  font-size: 20px;
}
.summary-empty h4 {
  margin: 0 0 6px;
  font-size: 15px;
  color: #1a1a1a;
}
.summary-empty p {
  margin: 0 0 18px;
  font-size: 13px;
  color: #9aa3ad;
}
.summary-empty-cta {
  border: none;
  background: #021c44;
  color: #fff;
  border-radius: 10px;
  padding: 11px 20px;
  font-size: 13px;
  font-weight: 600;
  cursor: pointer;
}
.summary-empty-cta:hover {
  background: #032d6b;
}

.summary-room {
  position: relative;
  padding-right: 26px;
  display: flex;
  gap: 14px;
  padding-bottom: max(18px, 1.25vw);
  border-bottom: 1px solid #edf0f4;
  margin-bottom: max(18px, 1.25vw);
}
.summary-room img {
  width: 76px;
  height: 76px;
  border-radius: 12px;
  object-fit: cover;
  flex-shrink: 0;
}
.summary-room-info {
  min-width: 0;
}
.summary-tag {
  display: inline-block;
  font-size: 10.5px;
  font-weight: 700;
  color: #1a51ad;
  background: #eef3fa;
  padding: 3px 9px;
  border-radius: 6px;
  letter-spacing: 0.3px;
  text-transform: uppercase;
  margin-bottom: 6px;
}
.summary-room-info h4 {
  margin: 0 0 4px;
  font-size: 15px;
  color: #1a1a1a;
  font-weight: 600;
}
.summary-room-info p {
  margin: 0;
  font-size: 12.5px;
  color: #7c8693;
}

.summary-remove {
  position: absolute;
  top: 0;
  right: 0;
  width: 26px;
  height: 26px;
  border-radius: 50%;
  border: 1px solid #e8edf3;
  background: #fff;
  color: #9aa3ad;
  display: inline-flex;
  align-items: center;
  justify-content: center;
  font-size: 11px;
  cursor: pointer;
  transition: background 0.15s ease, border-color 0.15s ease, color 0.15s ease;
}

.summary-remove:hover {
  background: #fdeeee;
  border-color: #f3c9c9;
  color: #e53935;
}

.summary-meta {
  display: flex;
  flex-direction: column;
  gap: 10px;
  padding-bottom: max(18px, 1.25vw);
  border-bottom: 1px solid #edf0f4;
  margin-bottom: max(18px, 1.25vw);
}
.summary-meta-row {
  display: flex;
  align-items: center;
  justify-content: space-between;
  font-size: 13px;
  color: #6b7280;
}
.summary-meta-row i {
  color: #1a51ad;
  width: 16px;
  margin-right: 6px;
}
.summary-meta-row strong {
  color: #1a1a1a;
  font-weight: 600;
}

.summary-breakdown {
  padding-bottom: max(18px, 1.25vw);
  border-bottom: 1px solid #edf0f4;
  margin-bottom: max(18px, 1.25vw);
}
.summary-row {
  display: flex;
  align-items: baseline;
  justify-content: space-between;
  gap: 10px;
  margin-top: 10px;
  font-size: 12.5px;
  color: #6b7280;
}
.summary-row:first-child {
  margin-top: 0;
}
.summary-total {
  margin-top: 14px;
  padding-top: 12px;
  border-top: 1px dashed #e7ebf0;
  font-size: 17px;
  font-weight: 700;
  color: #1a51ad;
}

.summary-trust {
  list-style: none;
  margin: 0 0 max(16px, 1.1111vw);
  padding: 0;
  display: flex;
  flex-direction: column;
  gap: 9px;
}
.summary-trust li {
  display: flex;
  align-items: flex-start;
  gap: 8px;
  font-size: 12.5px;
  color: #444;
  line-height: 1.5;
}
.summary-trust li i {
  color: #1b7a3d;
  margin-top: 2px;
  flex-shrink: 0;
}

.summary-secure {
  display: flex;
  align-items: center;
  gap: 8px;
  font-size: 11.5px;
  color: #9aa3ad;
  background: #f8f9fb;
  border-radius: 8px;
  padding: 10px 12px;
}
.summary-secure i {
  color: #6b7280;
}

/* RESPONSIVE */

@media (max-width: 992px) {
  .checkout-body {
    grid-template-columns: 1fr;
  }
  .summary-card {
    position: static;
    order: -1;
  }
}

@media (max-width: 768px) {
  .checkout-head h2 {
    font-size: 28px;
  }
  .field-row,
  .payment-row-3 {
    grid-template-columns: 1fr;
  }
  .form-card {
    padding: 22px 20px;
  }
  .step-label {
    display: none;
  }
  .step-track {
    max-width: 160px;
  }
  .payment-actions {
    flex-direction: column-reverse;
  }
  .checkout-back {
    width: 100%;
    justify-content: center;
  }
}
</style>