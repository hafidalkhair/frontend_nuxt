<script setup>
// Script kosong, hanya butuh navigasi
</script>

<template>
  <div class="container">
    <div class="blob blob-1"></div>
    <div class="blob blob-2"></div>

    <div class="card glass-effect">
      <div class="icon-wrapper"><span class="icon">🎓</span></div>
      <h1 class="title">Kampus App</h1>
      <p class="subtitle">Sistem Informasi Akademik Terpadu.<br>Platform digital masa depan.</p>
      
      <NuxtLink to="/login" class="link-no-decor">
        <button class="btn-primary">Masuk Aplikasi <span>→</span></button>
      </NuxtLink>

      <div class="footer"><p>🚀 Project API Laravel & Nuxt.js</p></div>
    </div>
  </div>
</template>

<style scoped>
/* Gunakan CSS Global untuk Container, Blob, Glass Effect */
.container { min-height: 100vh; display: flex; justify-content: center; align-items: center; background: linear-gradient(-45deg, #ee7752, #e73c7e, #23a6d5, #23d5ab); background-size: 400% 400%; animation: gradientBG 15s ease infinite; position: relative; overflow: hidden; padding: 20px; }
.blob { position: absolute; border-radius: 50%; z-index: 0; }
.blob-1 { top: 10%; left: 10%; width: 300px; height: 300px; background: rgba(255, 255, 255, 0.3); filter: blur(80px); }
.blob-2 { bottom: 10%; right: 10%; width: 350px; height: 350px; background: rgba(255, 255, 255, 0.2); filter: blur(100px); }
.card { background: rgba(255, 255, 255, 0.85); backdrop-filter: blur(20px); padding: 60px 40px; border-radius: 24px; box-shadow: 0 20px 50px rgba(0, 0, 0, 0.15); width: 100%; max-width: 450px; text-align: center; border: 1px solid rgba(255, 255, 255, 0.5); z-index: 1; animation: slideUp 0.8s cubic-bezier(0.2, 0.8, 0.2, 1); }
.icon-wrapper { font-size: 60px; margin-bottom: 20px; animation: float 3s infinite ease-in-out; }
.title { font-size: 36px; font-weight: 800; background: linear-gradient(to right, #2563eb, #9333ea); -webkit-background-clip: text; -webkit-text-fill-color: transparent; margin-bottom: 10px; }
.subtitle { color: #64748b; margin-bottom: 40px; line-height: 1.6; }
.btn-primary { width: 100%; padding: 16px; background: linear-gradient(135deg, #2563eb 0%, #1d4ed8 100%); color: white; border: none; border-radius: 14px; font-weight: 600; cursor: pointer; transition: 0.3s; font-size: 16px; }
.btn-primary:hover { transform: translateY(-2px); box-shadow: 0 10px 20px rgba(37, 99, 235, 0.4); }
.link-no-decor { text-decoration: none; }
.footer { margin-top: 30px; border-top: 1px solid rgba(0,0,0,0.05); padding-top: 20px; font-size: 13px; color: #94a3b8; }
/* Keyframes */
@keyframes gradientBG { 0% { background-position: 0% 50%; } 50% { background-position: 100% 50%; } 100% { background-position: 0% 50%; } }
@keyframes float { 0%, 100% { transform: translateY(0); } 50% { transform: translateY(-15px); } }
@keyframes slideUp { from { opacity: 0; transform: translateY(30px); } to { opacity: 1; transform: translateY(0); } }
</style>