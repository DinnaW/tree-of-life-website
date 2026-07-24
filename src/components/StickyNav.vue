<template>
  <div class="nav-wrapper" :class="{ 'is-stuck': isStuck }" ref="navWrapper">
    <nav class="container tabs">
      <a
        v-for="section in sections"
        :key="section.id"
        class="tab"
        :class="{ active: activeSection === section.id }"
        @click.prevent="scrollToSection(section.id)"
      >
        {{ section.label }}
      </a>
    </nav>
  </div>
  <!-- Keeps the page from jumping when the nav above switches to fixed -->
  <div v-if="isStuck" class="nav-spacer" :style="{ height: navHeight + 'px' }"></div>
</template>

<script setup>
import { ref, onMounted, onBeforeUnmount } from "vue";

const props = defineProps({
  sections: {
    type: Array,
    required: true, // [{ id, label }]
  },
});

const navWrapper = ref(null);
const isStuck = ref(false);
const activeSection = ref(props.sections[0]?.id ?? "");
const navOffsetTop = ref(0);
const navHeight = ref(0);

function handleResize() {
  if (!isStuck.value) {
    navHeight.value = navWrapper.value.offsetHeight;
  }
}

function handleScroll() {
  isStuck.value = window.scrollY >= navOffsetTop.value;

  const scrollPos = window.scrollY + (isStuck.value ? 120 : 200);
  const ids = props.sections.map((s) => s.id);
  for (let i = ids.length - 1; i >= 0; i--) {
    const el = document.getElementById(ids[i]);
    if (el && el.offsetTop <= scrollPos) {
      activeSection.value = ids[i];
      break;
    }
  }
}

function scrollToSection(id) {
  const el = document.getElementById(id);
  if (!el) return;
  const height = navWrapper.value.offsetHeight;
  const top = el.offsetTop - height - 16;
  window.scrollTo({ top, behavior: "smooth" });
}

onMounted(() => {
  navOffsetTop.value = navWrapper.value.offsetTop;
  navHeight.value = navWrapper.value.offsetHeight;
  window.addEventListener("scroll", handleScroll, { passive: true });
  window.addEventListener("resize", handleResize, { passive: true });
  handleScroll();
});

onBeforeUnmount(() => {
  window.removeEventListener("scroll", handleScroll);
  window.removeEventListener("resize", handleResize);
});

// Let a parent (e.g. the header's Search button) scroll to a section too.
defineExpose({ scrollToSection });
</script>

<style scoped>
.container {
  max-width: max(1240px, 86.1111vw);
  margin: 0 auto;
  padding: 0 max(34px, 2.3611vw);
}
.nav-wrapper {
  margin-top: 0;
  border-bottom: max(1px, 0.0694vw) solid #e5e5e5;
  background: #fff;
  z-index: 100;
}
.nav-wrapper.is-stuck {
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  margin-top: 0;
  box-shadow: 0 max(2px, 0.1389vw) max(14px, 0.9722vw) rgba(0, 0, 0, 0.09);
  animation: slideDown 0.18s ease-out;
}
@keyframes slideDown {
  from {
    transform: translateY(-100%);
  }
  to {
    transform: translateY(0);
  }
}
.tabs {
  display: flex;
  gap: max(80px, 5.5556vw);
}
.tab {
  padding: max(18px, 1.25vw) 0;
  font-size: max(13px, 0.9028vw);
  font-weight: 350;
  letter-spacing: max(0.7px, 0.0486vw);
  color: #6b6b6b;
  text-decoration: none;
  border-bottom: max(3px, 0.2083vw) solid transparent;
  transition: color 0.15s ease, border-color 0.15s ease;
  cursor: pointer;
}
.tab:hover {
  color: #03116e;
}
.tab.active {
  color: #03116e;
  border-bottom-color: #03116e;
}
</style>
