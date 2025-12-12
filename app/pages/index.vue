<script setup>
// Landing Page Logic
</script>

<template>
  <div class="screen">
    <div class="grid-pattern"></div>
    <div class="orb orb-1"></div>
    <div class="orb orb-2"></div>

    <div class="hero-card">
      <div class="icon-wrapper">🎓</div>
      <h1 class="title">Kampus<span class="highlight">App</span></h1>
      <p class="subtitle">Sistem Informasi Akademik Next-Gen.<br class="hide-mobile"/> Cepat, Aman, dan Futuristik.</p>

      <div class="action-area">
        <NuxtLink to="/login" class="link-no-decor">
          <button class="btn-neon">
            Login Portal <span class="arrow">→</span>
          </button>
        </NuxtLink>
      </div>

      <div class="tech-stack">
        <span>Powered by Nuxt 3 & Laravel</span>
      </div>
    </div>
  </div>
</template>

<style scoped>
.screen { min-height: 100vh; display: flex; justify-content: center; align-items: center; position: relative; overflow: hidden; padding: 20px; }
.grid-pattern { position: absolute; inset: 0; background-image: linear-gradient(rgba(255,255,255,0.03) 1px, transparent 1px), linear-gradient(90deg, rgba(255,255,255,0.03) 1px, transparent 1px); background-size: 50px 50px; z-index: 0; }
.orb { position: absolute; border-radius: 50%; filter: blur(120px); opacity: 0.4; z-index: 0; }
.orb-1 { width: 400px; height: 400px; background: #4f46e5; top: -100px; left: -100px; }
.orb-2 { width: 300px; height: 300px; background: #ec4899; bottom: -100px; right: -100px; }

.hero-card {
  position: relative; z-index: 10; text-align: center;
  background: rgba(15, 23, 42, 0.6); backdrop-filter: blur(20px);
  padding: 60px 40px; border-radius: 30px; border: 1px solid rgba(255,255,255,0.1);
  box-shadow: 0 0 0 1px rgba(255, 255, 255, 0.05), 0 20px 50px -10px rgba(0,0,0,0.5);
  max-width: 500px; width: 100%;
}

.icon-wrapper { font-size: 60px; margin-bottom: 20px; animation: float 6s ease-in-out infinite; }
.title { font-size: 48px; font-weight: 800; margin: 0 0 15px 0; letter-spacing: -2px; color: white; }
.highlight { background: linear-gradient(to right, #818cf8, #c084fc); -webkit-background-clip: text; -webkit-text-fill-color: transparent; }
.subtitle { color: #94a3b8; font-size: 18px; line-height: 1.6; margin-bottom: 40px; }

.link-no-decor { text-decoration: none; }
.btn-neon {
  padding: 16px 40px; background: white; color: #020617; border: none; border-radius: 100px;
  font-weight: 700; font-size: 16px; cursor: pointer; transition: 0.3s; display: inline-flex; align-items: center; gap: 10px;
}
.btn-neon:hover { transform: scale(1.05); box-shadow: 0 0 30px rgba(255,255,255,0.4); }
.tech-stack { margin-top: 40px; font-size: 12px; color: #64748b; text-transform: uppercase; letter-spacing: 2px; font-weight: 700; }

@keyframes float { 0%, 100% { transform: translateY(0); } 50% { transform: translateY(-15px); } }

/* MOBILE RESPONSIVE */
@media (max-width: 768px) {
  .hero-card { padding: 40px 20px; }
  .title { font-size: 36px; }
  .subtitle { font-size: 15px; }
  .hide-mobile { display: none; }
  .btn-neon { width: 100%; justify-content: center; }
}
</style>