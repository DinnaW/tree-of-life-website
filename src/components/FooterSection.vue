<template>
  <footer class="footer">
    <div class="container">
      <p class="copyright">
        © 2026 All Rights Reserved And Copyrighted.
      </p>

      <div class="right-content">
        <p class="credits">
          Web Design & Developed By Saasbeds.Com
        </p>
      </div>
    </div>
  </footer>
</template>

<script setup>
const scrollToTop = () => {
  window.scrollTo({
    top: 0,
    behavior: 'smooth'
  });
};
</script>

<style scoped>

*{
  overflow: hidden;
}

.footer {
  position: relative;
  width: 100%;
  background: #d9e8f7;
  padding: 18px 0;
  color: #ffffff;
}

.container {
  max-width: 1200px;
  margin: 0 auto;
  padding: 0 20px;
  display: flex;
  justify-content: space-between;
  align-items: center;
  position: relative;
}

.footer p{
    text-align:center;
    font-family:'Figtree',sans-serif;
    font-size:12px;
    font-weight:400;
    color:#2f2f2f;
    margin:0;
}

.right-content {
  display: flex;
  align-items: center;
  gap: 24px;
}

@media (max-width: 768px) {
  .container {
    flex-direction: column;
    gap: 12px;
    text-align: center;
  }

  .right-content {
    flex-direction: column;
    gap: 8px;
  }

  .scroll-top-btn {
    position: static;
    margin-top: 8px;
  }
}
</style>