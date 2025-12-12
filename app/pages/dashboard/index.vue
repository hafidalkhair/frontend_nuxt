<script setup>
import { ref, onMounted } from 'vue';
import { useRouter } from 'vue-router';

const router = useRouter();
const mahasiswa = ref([]);
const loading = ref(true);
const showModal = ref(false);
const deleteId = ref(null);
const userName = ref('');

onMounted(() => {
  const token = localStorage.getItem('token');
  const user = localStorage.getItem('user_name');
  
  if(!token) return router.push('/login');
  
  userName.value = user || 'Admin';

  fetch('https://hafid.copium.id/api/mahasiswa', { headers: { 'Authorization': `Bearer ${token}` } })
    .then(res => res.json())
    .then(data => { 
        mahasiswa.value = data.data || data; 
        loading.value=false; 
    });
});

// --- FUNGSI LOGOUT ---
const handleLogout = () => {
  if(confirm('Apakah Anda yakin ingin keluar?')) {
    localStorage.removeItem('token');
    localStorage.removeItem('user_name');
    router.push('/login');
  }
};

const confirmDelete = (id) => { deleteId.value = id; showModal.value = true; };
const executeDelete = async () => {
  const token = localStorage.getItem('token');
  await fetch(`https://hafid.copium.id/api/mahasiswa/${deleteId.value}`, { method: 'DELETE', headers: { 'Authorization': `Bearer ${token}` } });
  mahasiswa.value = mahasiswa.value.filter(m => m.id !== deleteId.value);
  showModal.value = false;
};
</script>

<template>
  <div class="dashboard-layout">
    <nav class="navbar">
      <div class="brand">Kampus<span class="dot">.</span></div>
      
      <div class="nav-right">
        <div class="user-profile">
          <div class="avatar-circle">{{ userName.charAt(0).toUpperCase() }}</div>
          <span class="role-text">{{ userName }}</span>
        </div>
        <button @click="handleLogout" class="btn-logout-nav">
          <span class="icon-log">⏻</span> <span class="text-log">Logout</span>
        </button>
      </div>
    </nav>

    <main class="main-content">
      <div class="stats-row">
        <div class="stat-card blue"><h3>Total Siswa</h3><p class="number">{{ mahasiswa.length }}</p><div class="icon">🎓</div></div>
        <div class="stat-card purple"><h3>Server</h3><p class="number">Online</p><div class="icon">⚡</div></div>
      </div>

      <div class="action-header">
        <div class="tabs">
          <button class="tab active">Mahasiswa</button>
          <NuxtLink to="/dashboard/dosen"><button class="tab">Dosen</button></NuxtLink>
        </div>
        <NuxtLink to="/dashboard/create" class="btn-link">
          <button class="btn-create">+ Data Baru</button>
        </NuxtLink>
      </div>

      <div class="data-list">
        <div v-if="loading" class="loader-text">Mengambil Data...</div>
        <div v-else class="list-wrapper">
          <div v-for="m in mahasiswa" :key="m.id" class="list-row">
            <div class="row-left">
              <div class="row-avatar">{{ m.nama.charAt(0) }}</div>
              <div class="row-info"><div class="name">{{ m.nama }}</div><div class="meta">{{ m.nim }} • {{ m.program_studi_id }}</div></div>
            </div>
            <div class="row-right">
              <div class="badge-wrapper"><span class="badge">{{ m.jenis_kelamin ? 'Laki-laki' : 'Perempuan' }}</span></div>
              <div class="actions">
                <NuxtLink :to="`/dashboard/edit/${m.id}`" class="icon-btn edit">Edit</NuxtLink>
                <button @click="confirmDelete(m.id)" class="icon-btn delete">Hapus</button>
              </div>
            </div>
          </div>
        </div>
      </div>
    </main>

    <div v-if="showModal" class="modal-overlay">
      <div class="modal-box">
        <h3>Hapus Data?</h3>
        <p>Data yang dihapus tidak dapat dikembalikan.</p>
        <div class="modal-actions">
          <button @click="showModal=false" class="btn-cancel">Batal</button>
          <button @click="executeDelete" class="btn-del">Hapus</button>
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped>
/* BASE */
.dashboard-layout { min-height: 100vh; background: #020617; padding-top: 80px; font-family: 'Plus Jakarta Sans', sans-serif; color: #e2e8f0; }

/* NAVBAR UPDATE */
.navbar { position: fixed; top: 0; left: 0; width: 100%; height: 70px; background: rgba(15,23,42,0.9); backdrop-filter: blur(10px); border-bottom: 1px solid #1e293b; display: flex; justify-content: space-between; align-items: center; padding: 0 40px; box-sizing: border-box; z-index: 100; }
.brand { font-size: 24px; font-weight: 800; color: #fff; } .dot { color: #6366f1; }

.nav-right { display: flex; align-items: center; gap: 20px; }
.user-profile { display: flex; gap: 10px; align-items: center; font-weight: 600; font-size: 14px; }
.avatar-circle { width: 32px; height: 32px; background: linear-gradient(135deg, #6366f1, #a855f7); border-radius: 50%; display: flex; justify-content: center; align-items: center; color: white; font-weight: bold; }

/* Tombol Logout Keren */
.btn-logout-nav {
  display: flex; align-items: center; gap: 8px;
  background: rgba(239, 68, 68, 0.1); border: 1px solid rgba(239, 68, 68, 0.2);
  color: #ef4444; padding: 8px 16px; border-radius: 8px; cursor: pointer; font-weight: 600; transition: 0.2s;
}
.btn-logout-nav:hover { background: #ef4444; color: white; }
.icon-log { font-size: 14px; }

/* MAIN CONTENT */
.main-content { max-width: 1000px; margin: 0 auto; padding: 20px; }
.stats-row { display: grid; grid-template-columns: 1fr 1fr; gap: 20px; margin-bottom: 30px; }
.stat-card { background: #0f172a; border: 1px solid #1e293b; border-radius: 16px; padding: 20px; position: relative; }
.stat-card h3 { color: #94a3b8; font-size: 12px; margin: 0; text-transform: uppercase; font-weight: 700; letter-spacing: 1px; }
.stat-card .number { font-size: 28px; font-weight: 800; color: white; margin: 5px 0 0 0; }
.stat-card .icon { position: absolute; right: 20px; bottom: 20px; font-size: 30px; opacity: 0.2; }
.stat-card.blue { border-top: 3px solid #6366f1; } .stat-card.purple { border-top: 3px solid #a855f7; }

/* ACTIONS */
.action-header { display: flex; justify-content: space-between; margin-bottom: 20px; align-items: center; }
.tabs { background: #0f172a; padding: 5px; border-radius: 10px; border: 1px solid #1e293b; display: flex; gap: 5px; }
.tab { padding: 8px 16px; background: transparent; color: #94a3b8; border: none; cursor: pointer; font-weight: 600; border-radius: 8px; transition: 0.2s; }
.tab.active { background: #1e293b; color: white; box-shadow: 0 2px 5px rgba(0,0,0,0.2); }
.btn-create { background: white; color: #020617; padding: 10px 20px; border-radius: 8px; border: none; font-weight: 700; cursor: pointer; }
.btn-link { text-decoration: none; }

/* LIST */
.data-list { background: #0f172a; border-radius: 16px; border: 1px solid #1e293b; overflow: hidden; }
.list-row { display: flex; justify-content: space-between; align-items: center; padding: 20px; border-bottom: 1px solid #1e293b; transition: 0.2s; }
.list-row:last-child { border-bottom: none; }
.list-row:hover { background: #162032; }
.row-left { display: flex; gap: 15px; align-items: center; }
.row-avatar { width: 45px; height: 45px; background: #1e293b; border-radius: 12px; display: flex; justify-content: center; align-items: center; font-weight: 800; color: #6366f1; font-size: 18px; flex-shrink: 0; }
.name { color: white; font-weight: 600; font-size: 15px; } 
.meta { color: #64748b; font-size: 13px; margin-top: 2px; }
.row-right { display: flex; align-items: center; gap: 20px; }
.badge { font-size: 12px; background: rgba(99,102,241,0.1); color: #818cf8; padding: 4px 10px; border-radius: 6px; border: 1px solid rgba(99,102,241,0.2); }
.actions { display: flex; gap: 10px; }
.icon-btn { padding: 6px 12px; border-radius: 6px; border: none; cursor: pointer; font-size: 12px; font-weight: 600; text-decoration: none; display: inline-block; }
.icon-btn.edit { background: #1e293b; color: #cbd5e1; border: 1px solid #334155; }
.icon-btn.delete { background: rgba(239, 68, 68, 0.1); color: #ef4444; border: 1px solid rgba(239, 68, 68, 0.2); }

/* MODAL & LOADER */
.modal-overlay { position: fixed; inset: 0; background: rgba(0,0,0,0.8); display: flex; justify-content: center; align-items: center; z-index: 200; backdrop-filter: blur(5px); }
.modal-box { background: #0f172a; padding: 30px; border-radius: 16px; text-align: center; color: white; border: 1px solid #334155; width: 90%; max-width: 320px; box-shadow: 0 20px 50px -10px rgba(0,0,0,0.5); }
.modal-actions { display: flex; gap: 10px; justify-content: center; margin-top: 20px; }
.btn-cancel { padding: 10px 20px; background: transparent; border: 1px solid #334155; color: #cbd5e1; border-radius: 8px; cursor: pointer; }
.btn-del { padding: 10px 20px; background: #ef4444; border: none; color: white; border-radius: 8px; cursor: pointer; }
.loader-text { padding: 40px; text-align: center; color: #64748b; }

/* RESPONSIVE */
@media (max-width: 768px) {
  .dashboard-layout { padding-top: 70px; padding-left: 15px; padding-right: 15px; }
  .navbar { padding: 0 20px; }
  .role-text { display: none; } /* Sembunyikan nama user di HP */
  .text-log { display: none; } /* Sembunyikan tulisan 'Logout', sisakan icon */
  .btn-logout-nav { padding: 8px 10px; } /* Perkecil tombol logout */
  
  .stats-row { grid-template-columns: 1fr; gap: 15px; margin-bottom: 20px; }
  .action-header { flex-direction: column; gap: 15px; align-items: stretch; }
  .tabs { justify-content: center; width: 100%; } .tab { flex: 1; text-align: center; } .btn-create { width: 100%; justify-content: center; padding: 12px; }
  
  .list-row { flex-direction: column; align-items: flex-start; padding: 20px; gap: 15px; }
  .row-left { width: 100%; margin-bottom: 5px; }
  .row-right { width: 100%; flex-direction: row; justify-content: space-between; align-items: center; border-top: 1px solid #1e293b; padding-top: 15px; }
  .actions { gap: 10px; } .icon-btn { padding: 8px 16px; font-size: 13px; }
}
</style>