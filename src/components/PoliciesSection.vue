<template>
  <section class="policies-section">
    <div class="container">
      <header class="section-head">
        
        <div class="eyebrow">
        05 — POLICIES
        </div>
        
        <h2>Things to know</h2>

        <p class="subhead">Everything to plan around before you arrive</p>
      </header>

      <div class="policies-grid">
        <div class="policy-card" v-for="policy in policies" :key="policy.title">
          <span class="policy-bar" aria-hidden="true"></span>
          <div class="policy-body">
            <h3>{{ policy.title }}</h3>
            <p>{{ policy.description }}</p>

            <div v-if="policy.payment" class="payment-icons">
              <Icon icon="simple-icons:visa" width="34" />
              <Icon icon="simple-icons:mastercard" width="34" />
            </div>
          </div>
        </div>
      </div>
    </div>
  </section>
</template>

<script setup>
import { Icon } from "@iconify/vue";

const policies = [
  {
    title: "Check-in & check-out",
    description:
      "Check-in time is from 2:00 PM (14:00 hrs.) onwards, and check-out time is 12:00 noon."
  },
  {
    title: "Early check-in",
    description:
      "Early arrivals are subject to availability and must be informed at the time of reservation."
  },
  {
    title: "Late departure",
    description:
      "Late departure up to 2:30 PM is available upon request. Later departures may incur additional charges."
  },
  {
    title: "Child policy",
    description:
      "Children below 5 years stay free with parents. Older children may incur additional charges."
  },
  {
    title: "Extra bed",
    description:
      "Extra beds can only be arranged for double rooms. Maximum one extra bed per room."
  },
  {
    title: "Paying at the hotel",
    description: "The following payment methods are accepted by the property.",
    payment: true
  },
  {
    title: "Age requirements",
    description: "Guests checking in must be at least 18 years old."
  },
  {
    title: "Pet policy",
    description: "Pets are not allowed."
  }
];
</script>

<style scoped>

*{
  margin:0;
  padding:0;
  box-sizing:border-box;
  font-family: 'Figtree';
}

.policies-section {
  --color-bg: #eef4fa;
  --color-ink: #2b2f36;
  --color-ink-soft: #6b7280;
  --color-line: #dce6f0;
  --color-blue-dark: #19509d;
  --color-blue-light: #2a6bb0;

  padding:50px 0;
  background: var(--color-bg);
  background:#f8f9fb;
}

.container {
  max-width: 1200px;
  margin: 0 auto;
  padding: 0 20px;
}

/* SECTION HEAD*/

.eyebrow{
    color:#034acf;
    font-size:10px;
    font-weight:700;
    letter-spacing:1px;
    margin-bottom:10px;
}

.section-head {
  margin-bottom: 56px;
}

.section-head h2 {
  font-size:34px;
  font-weight:500;
  color:#1A51AD;
  margin-bottom:30px;
  line-height:1.3;
}


.subhead {
  margin: 0;
  font-size: 15px;
  color: var(--color-ink-soft);
}

/* GRID */

.policies-grid {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  gap: 40px 48px;
}

/* CARD */

.policy-card {
  display: flex;
  gap: 16px;
}

.policy-bar {
  flex: 0 0 3px;
  border-radius: 2px;
  background: var(--color-line);
  transition: background 0.2s ease;
}

.policy-card:hover .policy-bar {
  background: var(--color-blue-light);
}

.policy-body h3 {
  margin: 0 0 8px;

  font-size: 18px;
  font-weight: 500;
  color: var(--color-blue-dark);
}

.policy-body p {
  margin: 0;
  font-size: 14px;
  line-height: 1.7;
  color: var(--color-ink-soft);
}

/* PAYMENT ICONS */

.payment-icons {
  display: flex;
  gap: 14px;
  margin-top: 18px;
  color: var(--color-blue-dark);
}

/* RESPONSIVE */

@media (max-width: 992px) {
  .policies-grid {
    grid-template-columns: repeat(2, 1fr);
  }
}

@media (max-width: 768px) {
  .policies-section {
    padding: 48px 16px;
    border-radius: 0;
  }

  .policies-grid {
    grid-template-columns: 1fr;
    gap: 32px;
  }

  .section-head h2 {
    font-size: 32px;
  }
}
</style>