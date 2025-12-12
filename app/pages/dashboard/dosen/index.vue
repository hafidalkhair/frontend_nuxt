<script setup>
import { ref, onMounted } from 'vue';
import { useRouter } from 'vue-router';
const router = useRouter();
const dosens = ref([]);
const loading = ref(true);

onMounted(() => {
  const token = localStorage.getItem('token');
  if(!token) return router.push('/login');
  fetch('https://hafid.copium.id/api/dosens', { headers: { 'Authorization': `Bearer ${token}` } })
    .then(res => res.json()).then(data => { dosens.value = data.data || data; loading.value=false; });
});
</script>

<template>
  <div class="dashboard-layout">
    <nav class="navbar">
      <div class="brand">Kampus<span class="dot">.</span></div>
      <div class="user-profile"><div class="avatar-circle">A</div><span>Admin</span></div>
    </nav>

    <main class="main-content">
      <div class="stats-row">
        <div class="stat-card blue">
          <h3>Total Dosen</h3><p class="number">{{ dosens.length }}</p><div class="icon">👨‍🏫</div>
        </div>
        <div class="stat-card pink">
          <h3>Status</h3><p class="number">Active</p><div class="icon">⚡</div>
        </div>
      </div>

      <div class="action-header">
        <div class="tabs">
          <NuxtLink to="/dashboard"><button class="tab">Mahasiswa</button></NuxtLink>
          <button class="tab active">Dosen</button>
        </div>
        <NuxtLink to="/dashboard/dosen/create">
          <button class="btn-create">+ New Dosen</button>
        </NuxtLink>
      </div>

      <div class="data-list">
        <div v-if="loading" class="loader-text">Loading Dosen Data...</div>
        <div v-else class="list-wrapper">
          <div v-for="d in dosens" :key="d.id" class="list-row">
            <div class="row-left">
              <div class="row-avatar pink">{{ d.nama_lengkap.charAt(0) }}</div>
              <div class="row-info">
                <div class="name">{{ d.nama_lengkap }}</div>
                <div class="meta">{{ d.nidn || d.nip || 'No ID' }} • {{ d.jabatan_fungsional }}</div>
              </div>
            </div>
            <div class="row-right">
              <div class="badge" :class="d.status === 'aktif' ? 'green' : 'red'">{{ d.status }}</div>
              <div class="actions">
                <NuxtLink :to="`/dashboard/dosen/edit/${d.id}`" class="icon-btn edit">✎</NuxtLink>
                <button class="icon-btn delete">🗑</button>
              </div>
            </div>
          </div>
        </div>
      </div>
    </main>
  </div>
</template>

<style scoped>
/* CSS Dashboard Dark (Sama dengan dashboard/index.vue) */
.dashboard-layout { min-height: 100vh; background-color: #020617; color: #e2e8f0; padding-top: 80px; }
.navbar { position: fixed; top: 0; left: 0; width: 100%; height: 70px; background: rgba(15, 23, 42, 0.8); backdrop-filter: blur(10px); border-bottom: 1px solid #1e293b; display: flex; justify-content: space-between; align-items: center; padding: 0 40px; box-sizing: border-box; z-index: 100; }
.brand { font-size: 24px; font-weight: 800; color: #fff; } .dot { color: #6366f1; } .user-profile { display: flex; align-items: center; gap: 10px; font-size: 14px; font-weight: 600; }
.avatar-circle { width: 35px; height: 35px; background: linear-gradient(135deg, #6366f1, #a855f7); border-radius: 50%; display: flex; align-items: center; justify-content: center; color: white; }
.main-content { max-width: 1000px; margin: 0 auto; padding: 20px; }
.stats-row { display: grid; grid-template-columns: 1fr 1fr; gap: 20px; margin-bottom: 40px; }
.stat-card { background: #0f172a; border: 1px solid #1e293b; border-radius: 16px; padding: 25px; position: relative; overflow: hidden; transition: 0.3s; }
.stat-card:hover { transform: translateY(-5px); border-color: #334155; }
.stat-card h3 { color: #94a3b8; font-size: 14px; margin: 0; text-transform: uppercase; letter-spacing: 1px; }
.stat-card .number { font-size: 32px; font-weight: 800; margin: 10px 0 0 0; color: #fff; }
.stat-card .icon { position: absolute; right: 20px; bottom: 20px; font-size: 40px; opacity: 0.2; }
.stat-card.blue::before { content:''; position: absolute; top:0; left:0; width: 100%; height: 4px; background: #6366f1; }
.stat-card.pink::before { content:''; position: absolute; top:0; left:0; width: 100%; height: 4px; background: #ec4899; }
.action-header { display: flex; justify-content: space-between; align-items: center; margin-bottom: 20px; }
.tabs { background: #0f172a; padding: 5px; border-radius: 12px; display: flex; gap: 5px; border: 1px solid #1e293b; }
.tab { padding: 8px 20px; background: transparent; color: #94a3b8; border: none; border-radius: 8px; cursor: pointer; font-weight: 600; transition: 0.3s; }
.tab.active { background: #1e293b; color: #fff; box-shadow: 0 2px 10px rgba(0,0,0,0.2); }
.btn-create { background: #fff; color: #020617; padding: 10px 20px; border-radius: 10px; border: none; font-weight: 700; cursor: pointer; transition: 0.3s; }
.btn-create:hover { background: #e2e8f0; }
.data-list { background: #0f172a; border-radius: 20px; border: 1px solid #1e293b; overflow: hidden; }
.list-row { display: flex; justify-content: space-between; align-items: center; padding: 20px; border-bottom: 1px solid #1e293b; transition: 0.2s; }
.list-row:last-child { border-bottom: none; }
.list-row:hover { background: #152036; }
.row-left { display: flex; gap: 15px; align-items: center; }
.row-avatar { width: 40px; height: 40px; background: #1e293b; border-radius: 10px; display: flex; align-items: center; justify-content: center; font-weight: bold; color: #6366f1; }
.row-avatar.pink { color: #ec4899; }
.row-info .name { font-weight: 600; font-size: 16px; color: #f1f5f9; }
.row-info .meta { font-size: 12px; color: #64748b; margin-top: 2px; }
.row-right { display: flex; align-items: center; gap: 20px; }
.badge { font-size: 12px; padding: 4px 10px; border-radius: 6px; border: 1px solid transparent; }
.badge.green { background: rgba(16, 185, 129, 0.1); color: #34d399; border-color: rgba(16, 185, 129, 0.2); }
.badge.red { background: rgba(239, 68, 68, 0.1); color: #f87171; border-color: rgba(239, 68, 68, 0.2); }
.actions { display: flex; gap: 10px; }
.icon-btn { width: 32px; height: 32px; border-radius: 8px; border: none; cursor: pointer; display: flex; align-items: center; justify-content: center; transition: 0.2s; background: #1e293b; color: #94a3b8; }
.icon-btn:hover { background: #334155; color: white; }
.icon-btn.delete:hover { background: #ef4444; }
.loader-text { padding: 40px; text-align: center; color: #64748b; }
</style>