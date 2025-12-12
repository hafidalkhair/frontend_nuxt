<script setup>
import { ref } from 'vue';
import { useRouter } from 'vue-router';
const router = useRouter();
const email = ref('');
const password = ref('');
const loading = ref(false);

const handleLogin = async () => {
  loading.value = true;
  // Simulasi delay biar kelihatan animasinya
  setTimeout(async () => {
    try {
      const res = await fetch('https://hafid.copium.id/api/login', {
        method: 'POST', headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify({ email: email.value, password: password.value }),
      });
      const data = await res.json();
      if (data.success || data.token) {
        if(process.client) {
            localStorage.setItem('token', data.token);
            if(data.user) localStorage.setItem('user_name', data.user.name);
        }
        router.push('/dashboard');
      } else { alert(data.message || 'Login gagal'); }
    } catch { alert('Error koneksi'); } 
    finally { loading.value = false; }
  }, 1000);
};
</script>

<template>
  <div class="screen">
    <div class="orb orb-1"></div>
    <div class="orb orb-2"></div>
    <div class="grid-pattern"></div>

    <div class="login-box">
      <div class="glow-border"></div>
      <div class="content">
        <div class="logo-area">
          <div class="logo-icon">🚀</div>
          <h1>Kampus<span class="highlight">App</span></h1>
          <p>Access the Future of Education</p>
        </div>

        <form @submit.prevent="handleLogin">
          <div class="input-group">
            <input v-model="email" type="email" required placeholder=" " />
            <label>Email Address</label>
            <div class="line"></div>
          </div>

          <div class="input-group">
            <input v-model="password" type="password" required placeholder=" " />
            <label>Password</label>
            <div class="line"></div>
          </div>

          <button type="submit" class="btn-neon" :class="{ loading }">
            <span v-if="!loading">Initialize Session</span>
            <span v-else class="loader"></span>
          </button>
        </form>
      </div>
    </div>
  </div>
</template>

<style scoped>
/* --- BACKGROUND & LAYOUT --- */
.screen {
  min-height: 100vh;
  display: flex;
  justify-content: center;
  align-items: center;
  position: relative;
  overflow: hidden;
  background: #020617;
}

/* Grid Pattern ala Tech */
.grid-pattern {
  position: absolute; width: 100%; height: 100%;
  background-image: linear-gradient(rgba(255, 255, 255, 0.03) 1px, transparent 1px),
  linear-gradient(90deg, rgba(255, 255, 255, 0.03) 1px, transparent 1px);
  background-size: 50px 50px;
  z-index: 0;
}

/* Glowing Orbs */
.orb {
  position: absolute; border-radius: 50%; filter: blur(100px); z-index: 0;
  animation: float 10s infinite alternate;
}
.orb-1 { width: 400px; height: 400px; background: #4f46e5; top: -100px; left: -100px; opacity: 0.4; }
.orb-2 { width: 300px; height: 300px; background: #ec4899; bottom: -50px; right: -50px; opacity: 0.3; animation-delay: -5s; }

/* --- LOGIN BOX (The Hero) --- */
.login-box {
  position: relative;
  width: 100%; max-width: 400px;
  background: rgba(15, 23, 42, 0.6);
  backdrop-filter: blur(20px);
  border-radius: 24px;
  z-index: 10;
  padding: 4px; /* Space for gradient border */
  box-shadow: 0 25px 50px -12px rgba(0, 0, 0, 0.5);
}

/* Gradient Border Effect */
.login-box::before {
  content: ""; position: absolute; inset: 0; border-radius: 24px; padding: 2px;
  background: linear-gradient(135deg, #6366f1, #a855f7, #ec4899);
  -webkit-mask: linear-gradient(#fff 0 0) content-box, linear-gradient(#fff 0 0);
  -webkit-mask-composite: xor;
  mask-composite: exclude;
  pointer-events: none;
}

.content {
  background: #0f172a;
  border-radius: 20px;
  padding: 40px;
  position: relative;
}

/* Typography */
.logo-area { text-align: center; margin-bottom: 40px; }
.logo-icon { font-size: 48px; margin-bottom: 10px; animation: bounce 2s infinite; }
.logo-area h1 { margin: 0; font-size: 32px; font-weight: 800; letter-spacing: -1px; }
.highlight { background: linear-gradient(to right, #818cf8, #c084fc); -webkit-background-clip: text; -webkit-text-fill-color: transparent; }
.logo-area p { color: #94a3b8; margin-top: 5px; font-size: 14px; }

/* Input Fields (Floating Label Style) */
.input-group { position: relative; margin-bottom: 25px; }
.input-group input {
  width: 100%; padding: 12px 0; font-size: 16px; color: #fff;
  background: transparent; border: none; outline: none;
  border-bottom: 1px solid #334155; transition: 0.3s;
  box-sizing: border-box; /* Fix CSS conflict */
}
.input-group label {
  position: absolute; top: 12px; left: 0; color: #64748b; font-size: 16px;
  pointer-events: none; transition: 0.3s ease;
}
/* Efek saat input diisi/fokus */
.input-group input:focus ~ label,
.input-group input:not(:placeholder-shown) ~ label {
  top: -10px; font-size: 12px; color: #818cf8;
}
.input-group input:focus { border-bottom-color: #818cf8; }

/* Line Animation */
.line {
  position: absolute; bottom: 0; left: 0; width: 0; height: 2px;
  background: #818cf8; transition: 0.3s;
}
.input-group input:focus ~ .line { width: 100%; }

/* Neon Button */
.btn-neon {
  width: 100%; padding: 16px; margin-top: 10px;
  background: linear-gradient(90deg, #4f46e5, #9333ea);
  color: white; border: none; border-radius: 12px;
  font-weight: 700; font-size: 16px; cursor: pointer;
  position: relative; overflow: hidden; transition: 0.3s;
  text-transform: uppercase; letter-spacing: 1px;
}
.btn-neon:hover {
  box-shadow: 0 0 20px rgba(79, 70, 229, 0.6);
  transform: translateY(-2px);
}
.btn-neon::after {
  content: ''; position: absolute; top: 0; left: -100%; width: 100%; height: 100%;
  background: linear-gradient(90deg, transparent, rgba(255,255,255,0.2), transparent);
  transition: 0.5s;
}
.btn-neon:hover::after { left: 100%; }

/* Animations */
@keyframes float { 0% { transform: translate(0,0); } 100% { transform: translate(20px, 20px); } }
@keyframes bounce { 0%, 100% { transform: translateY(0); } 50% { transform: translateY(-10px); } }
.loader { display: inline-block; width: 20px; height: 20px; border: 3px solid rgba(255,255,255,0.3); border-radius: 50%; border-top-color: #fff; animation: spin 1s ease-in-out infinite; }
@keyframes spin { to { transform: rotate(360deg); } }
</style>