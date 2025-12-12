<script setup>
import { ref } from 'vue';
import { useRouter } from 'vue-router';
const router = useRouter();
const email = ref('');
const password = ref('');
const loading = ref(false);

const handleLogin = async () => {
  loading.value = true;
  try {
    const res = await fetch('https://hafid.copium.id/api/login', {
      method: 'POST', headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify({ email: email.value, password: password.value }),
    });
    const data = await res.json();
    if (data.success || data.token) {
      if(process.client) {
          localStorage.setItem('token', data.token || data.access_token);
          if(data.user) localStorage.setItem('user_name', data.user.name);
      }
      router.push('/dashboard');
    } else { alert(data.message || 'Login gagal'); }
  } catch { alert('Koneksi Error'); } 
  finally { loading.value = false; }
};
</script>

<template>
  <div class="screen">
    <div class="orb orb-1"></div>
    <div class="login-box">
      <div class="content">
        <div class="header">
          <div class="icon">🔐</div>
          <h1>Welcome Back</h1>
          <p>Login to access dashboard</p>
        </div>
        <form @submit.prevent="handleLogin">
          <div class="input-group">
            <input v-model="email" type="email" required placeholder=" " />
            <label>Email Address</label>
          </div>
          <div class="input-group">
            <input v-model="password" type="password" required placeholder=" " />
            <label>Password</label>
          </div>
          <button type="submit" class="btn-neon" :disabled="loading">
            {{ loading ? 'Processing...' : 'Sign In' }}
          </button>
        </form>
      </div>
    </div>
  </div>
</template>

<style scoped>
.screen { min-height: 100vh; display: flex; justify-content: center; align-items: center; position: relative; overflow: hidden; background: #020617; padding: 20px; }
.orb-1 { position: absolute; width: 500px; height: 500px; background: #6366f1; border-radius: 50%; filter: blur(150px); opacity: 0.3; top: -100px; left: -100px; }

.login-box { width: 100%; max-width: 400px; background: rgba(15, 23, 42, 0.6); backdrop-filter: blur(20px); border-radius: 24px; border: 1px solid rgba(255,255,255,0.1); padding: 5px; box-shadow: 0 25px 50px -12px rgba(0,0,0,0.5); }
.content { background: #0f172a; border-radius: 20px; padding: 40px; }

.header { text-align: center; margin-bottom: 30px; }
.header .icon { font-size: 40px; margin-bottom: 10px; }
.header h1 { font-size: 28px; font-weight: 800; margin: 0; color: white; }
.header p { color: #64748b; margin: 5px 0 0 0; font-size: 14px; }

.input-group { position: relative; margin-bottom: 25px; }
.input-group input { width: 100%; padding: 12px 0; font-size: 16px; color: #fff; background: transparent; border: none; border-bottom: 1px solid #334155; outline: none; transition: 0.3s; box-sizing: border-box; }
.input-group label { position: absolute; top: 12px; left: 0; color: #64748b; font-size: 16px; pointer-events: none; transition: 0.3s ease; }
.input-group input:focus ~ label, .input-group input:not(:placeholder-shown) ~ label { top: -10px; font-size: 12px; color: #818cf8; }
.input-group input:focus { border-bottom-color: #818cf8; }

.btn-neon { width: 100%; padding: 16px; background: linear-gradient(90deg, #4f46e5, #9333ea); color: white; border: none; border-radius: 12px; font-weight: 700; cursor: pointer; transition: 0.3s; }
.btn-neon:hover:not(:disabled) { box-shadow: 0 0 20px rgba(79, 70, 229, 0.6); transform: translateY(-2px); }
.btn-neon:disabled { background: #334155; cursor: not-allowed; }

/* MOBILE */
@media (max-width: 480px) {
  .content { padding: 30px 20px; }
}
</style>