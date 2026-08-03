<template>
  <div class="faq-section">

    <div class="section-header">
      <div class="eyebrow">
         — FAQ
      </div>

      <h2>
        Frequently asked questions
      </h2>

      <p class="subhead">
        Everything you need to know before you stay with us.
      </p>

      <div class="faq-columns">
        <div class="faq-col">
          <div
            v-for="item in leftColumn"
            :key="item.i"
            class="faq-item"
            :class="{ open: openIndex === item.i }"
          >
            <button
              class="faq-question"
              :aria-expanded="openIndex === item.i"
              @click="toggle(item.i)"
            >
              <span>{{ item.question }}</span>
              <i class="fa-solid fa-plus toggle-icon"></i>
            </button>

            <div class="faq-answer-wrap">
              <div class="faq-answer">
                <p>{{ item.answer }}</p>
              </div>
            </div>
          </div>
        </div>

        <div class="faq-col">
          <div
            v-for="item in rightColumn"
            :key="item.i"
            class="faq-item"
            :class="{ open: openIndex === item.i }"
          >
            <button
              class="faq-question"
              :aria-expanded="openIndex === item.i"
              @click="toggle(item.i)"
            >
              <span>{{ item.question }}</span>
              <i class="fa-solid fa-plus toggle-icon"></i>
            </button>

            <div class="faq-answer-wrap">
              <div class="faq-answer">
                <p>{{ item.answer }}</p>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>

  </div>
</template>

<script setup>
import { ref, computed } from 'vue'

const openIndex = ref(0)

function toggle(idx) {
  openIndex.value = openIndex.value === idx ? null : idx
}

const faqs = [
  {
    question: 'What time is check-in and check-out?',
    answer:
      'Check-in starts at 2:00 PM and check-out is until 11:00 AM. Early check-in and late check-out can be arranged in advance, subject to availability.',
  },
  {
    question: 'Is breakfast included in the room rate?',
    answer:
      'Yes, a complimentary breakfast buffet is included with most room types. Half board and other meal plans are available as add-ons during booking.',
  },
  {
    question: 'Do you offer free cancellation?',
    answer:
      'Most rooms can be cancelled free of charge up to 48 hours before check-in. Non-refundable rates are discounted but are not eligible for a refund.',
  },
  {
    question: 'Is parking available on site?',
    answer:
      'Yes, we offer free private parking for all guests, available on site with no reservation required.',
  },
  {
    question: 'Are pets allowed?',
    answer:
      'Small pets are welcome in select rooms with prior notice. Please contact us directly before booking so we can confirm availability and any additional charges.',
  },
  {
    question: 'Do you provide airport transfers?',
    answer:
      'Airport transfers can be arranged for an additional charge. We recommend booking this at least 24 hours in advance so we can coordinate pickup times.',
  },
  {
    question: 'What amenities are available on site?',
    answer:
      'Guests have access to our restaurant, infinity pool, free WiFi throughout the property, and Ayurveda treatments — all available during your stay.',
  },
]

const indexed = faqs.map((item, i) => ({ ...item, i }))
const leftColumn = computed(() => indexed.filter((_, i) => i % 2 === 0))
const rightColumn = computed(() => indexed.filter((_, i) => i % 2 === 1))
</script>

<style scoped>

* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
  font-family: 'Figtree';
}

.faq-section {
  width: 100%;
  padding: max(50px, 3.4722vw) 0;
  background: #ffffff;
}

.section-header {
  max-width: max(1200px, 83.3333vw);
  margin: auto;
  padding: 0 max(20px, 1.3889vw);
}

.eyebrow {
  color: #034acf;
  font-size: clamp(max(10px, 0.6944vw), 0.8vw, max(12px, 0.8333vw));
  font-weight: 700;
  letter-spacing: max(1px, 0.0694vw);
  margin-bottom: clamp(max(8px, 0.5556vw), 1vw, max(12px, 0.8333vw));
}

.section-header h2{
  font-size:max(34px, 2.3611vw);
  font-weight:500;
  color:#1A51AD;
  margin-bottom: max(80px, 5.5556vw);
  line-height:1.3;
}

.subhead {
  font-size: max(14px, 0.9722vw);
  color: #6b7280;
  margin-bottom: max(30px, 2.0833vw);
}

/* FAQ LIST */

.faq-columns {
  display: flex;
  gap: max(14px, 0.9722vw);
  align-items: flex-start;
}

.faq-col {
  flex: 1;
  display: flex;
  flex-direction: column;
  gap: max(14px, 0.9722vw);
  min-width: 0;
}

.faq-item {
  background: #fff;
  border: max(1px, 0.0694vw) solid #e8edf3;
  border-radius: max(16px, 1.1111vw);
  overflow: hidden;
  transition: .3s;
}

.faq-item:hover {
  border-color: #1A51AD;
}

.faq-item.open {
  box-shadow: 0 max(10px, 0.6944vw) max(25px, 1.7361vw) rgba(0, 0, 0, .06);
}

.faq-question {
  all: unset;
  width: 100%;
  display: flex;
  align-items: center;
  justify-content: space-between;
  gap: max(16px, 1.1111vw);
  padding: max(20px, 1.3889vw) max(24px, 1.6667vw);
  cursor: pointer;
  box-sizing: border-box;
}

.faq-question span {
  font-size: max(15px, 1.0417vw);
  font-weight: 600;
  color: #222;
  line-height: 1.5;
}

.toggle-icon {
  flex-shrink: 0;
  width: max(30px, 2.0833vw);
  height: max(30px, 2.0833vw);
  border-radius: 50%;
  background: #f3f6ff;
  color: #034acf;
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: max(12px, 0.8333vw);
  transition: transform .3s ease, background .3s ease, color .3s ease;
}

.faq-item.open .toggle-icon {
  transform: rotate(135deg);
  background: #1A51AD;
  color: #fff;
}

/* SMOOTH GRID-BASED COLLAPSE */

.faq-answer-wrap {
  display: grid;
  grid-template-rows: 0fr;
  transition: grid-template-rows .3s ease;
}

.faq-item.open .faq-answer-wrap {
  grid-template-rows: 1fr;
}

.faq-answer {
  overflow: hidden;
}

.faq-answer p {
  padding: 0 max(24px, 1.6667vw) max(22px, 1.5278vw);
  font-size: max(13.5px, 0.9375vw);
  color: #555;
  line-height: 1.8;
}

/* Tablet */

@media (max-width: 992px) {
  .section-header h2 {
    font-size: 30px;
  }

  .faq-columns {
    flex-direction: column;
  }
}

/* Mobile */

@media (max-width: 768px) {
  .faq-section {
    padding: 40px 0;
  }

  .section-header {
    padding: 0 15px;
  }

  .section-header h2 {
    font-size: 26px;
  }

  .faq-question {
    padding: 16px 18px;
  }

  .faq-question span {
    font-size: 14px;
  }

  .faq-answer p {
    padding: 0 18px 18px;
  }
}
</style>