<script setup>
import { ref, onMounted } from 'vue';
import { useRouter } from 'vue-router';
const router = useRouter();
const mahasiswa = ref([]);
const loading = ref(true);
const showModal = ref(false);
const deleteId = ref(null);

onMounted(() => {
  const token = localStorage.getItem('token');
  if(!token) return router.push('/login');
  fetch('https://hafid.copium.id/api/mahasiswa', { headers: { 'Authorization': `Bearer ${token}` } })
    .then(res => res.json()).then(data => { mahasiswa.value = data.data || data; loading.value=false; });
});

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
      <div class="user-profile"><div class="avatar-circle">A</div><span class="role-text">Admin</span></div>
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
        <NuxtLink to="/dashboard/create" class="btn-link"><button class="btn-create">+ Add New</button></NuxtLink>
      </div>

      <div class="data-list">
        <div v-if="loading" class="loader-text">Loading...</div>
        <div v-else class="list-wrapper">
          <div v-for="m in mahasiswa" :key="m.id" class="list-row">
            <div class="row-left">
              <div class="row-avatar">{{ m.nama.charAt(0) }}</div>
              <div class="row-info"><div class="name">{{ m.nama }}</div><div class="meta">{{ m.nim }} • {{ m.program_studi_id }}</div></div>
            </div>
            <div class="row-right">
              <div class="badge">{{ m.jenis_kelamin ? 'Laki' : 'Pr' }}</div>
              <div class="actions">
                <NuxtLink :to="`/dashboard/edit/${m.id}`" class="icon-btn edit">✎</NuxtLink>
                <button @click="confirmDelete(m.id)" class="icon-btn delete">🗑</button>
              </div>
            </div>
          </div>
        </div>
      </div>
    </main>

    <div v-if="showModal" class="modal-overlay">
      <div class="modal-box">
        <h3>Hapus Data?</h3>
        <p>Data tidak bisa dikembalikan.</p>
        <div class="modal-actions">
          <button @click="showModal=false" class="btn-cancel">Batal</button>
          <button @click="executeDelete" class="btn-del">Hapus</button>
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped>
/* Base Styles */
.dashboard-layout { min-height: 100vh; background: #020617; padding-top: 80px; font-family: sans-serif; }
.navbar { position: fixed; top: 0; left: 0; width: 100%; height: 70px; background: rgba(15,23,42,0.9); backdrop-filter: blur(10px); border-bottom: 1px solid #1e293b; display: flex; justify-content: space-between; align-items: center; padding: 0 40px; box-sizing: border-box; z-index: 100; }
.brand { font-size: 24px; font-weight: 800; color: #fff; } .dot { color: #6366f1; } .user-profile { display: flex; gap: 10px; align-items: center; color: white; font-weight: 600; }
.avatar-circle { width: 35px; height: 35px; background: linear-gradient(135deg, #6366f1, #a855f7); border-radius: 50%; display: flex; justify-content: center; align-items: center; }
.main-content { max-width: 1000px; margin: 0 auto; padding: 20px; }

/* Stats */
.stats-row { display: grid; grid-template-columns: 1fr 1fr; gap: 20px; margin-bottom: 30px; }
.stat-card { background: #0f172a; border: 1px solid #1e293b; border-radius: 16px; padding: 20px; position: relative; }
.stat-card h3 { color: #94a3b8; font-size: 12px; margin: 0; text-transform: uppercase; }
.stat-card .number { font-size: 28px; font-weight: 800; color: white; margin: 5px 0 0 0; }
.stat-card .icon { position: absolute; right: 20px; bottom: 20px; font-size: 30px; opacity: 0.2; }
.stat-card.blue { border-top: 3px solid #6366f1; } .stat-card.purple { border-top: 3px solid #a855f7; }

/* Actions */
.action-header { display: flex; justify-content: space-between; margin-bottom: 20px; flex-wrap: wrap; gap: 15px; }
.tabs { background: #0f172a; padding: 5px; border-radius: 10px; border: 1px solid #1e293b; display: flex; gap: 5px; }
.tab { padding: 8px 16px; background: transparent; color: #94a3b8; border: none; cursor: pointer; font-weight: 600; border-radius: 8px; }
.tab.active { background: #1e293b; color: white; }
.btn-create { background: white; color: #020617; padding: 10px 20px; border-radius: 8px; border: none; font-weight: 700; cursor: pointer; }

/* List */
.data-list { background: #0f172a; border-radius: 16px; border: 1px solid #1e293b; overflow: hidden; }
.list-row { display: flex; justify-content: space-between; align-items: center; padding: 20px; border-bottom: 1px solid #1e293b; }
.list-row:last-child { border-bottom: none; }
.row-left { display: flex; gap: 15px; align-items: center; }
.row-avatar { width: 40px; height: 40px; background: #1e293b; border-radius: 10px; display: flex; justify-content: center; align-items: center; font-weight: bold; color: #6366f1; flex-shrink: 0;}
.name { color: white; font-weight: 600; } .meta { color: #64748b; font-size: 12px; }
.row-right { display: flex; align-items: center; gap: 15px; }
.badge { font-size: 11px; background: rgba(99,102,241,0.1); color: #818cf8; padding: 4px 8px; border-radius: 6px; }
.actions { display: flex; gap: 8px; }
.icon-btn { width: 32px; height: 32px; background: #1e293b; border: none; border-radius: 8px; color: #94a3b8; cursor: pointer; display: flex; justify-content: center; align-items: center; text-decoration: none;}
.icon-btn.delete:hover { background: #ef4444; color: white; }

/* Modal */
.modal-overlay { position: fixed; inset: 0; background: rgba(0,0,0,0.7); display: flex; justify-content: center; align-items: center; z-index: 200; backdrop-filter: blur(5px); }
.modal-box { background: #1e293b; padding: 30px; border-radius: 16px; text-align: center; color: white; border: 1px solid #334155; width: 90%; max-width: 320px; }
.modal-actions { display: flex; gap: 10px; justify-content: center; margin-top: 20px; }
.btn-cancel { padding: 10px 20px; background: transparent; border: 1px solid #64748b; color: #cbd5e1; border-radius: 8px; cursor: pointer; }
.btn-del { padding: 10px 20px; background: #ef4444; border: none; color: white; border-radius: 8px; cursor: pointer; }
.loader-text { padding: 20px; text-align: center; color: #64748b; }

/* MOBILE RESPONSIVE */
@media (max-width: 768px) {
  .dashboard-layout { padding-top: 70px; }
  .navbar { padding: 0 20px; } .role-text { display: none; }
  .stats-row { grid-template-columns: 1fr; }
  .action-header { flex-direction: column; gap: 10px; }
  .tabs { width: 100%; justify-content: center; } .btn-create-link { width: 100%; } .btn-create { width: 100%; }
  .list-row { flex-direction: column; align-items: flex-start; gap: 15px; }
  .row-right { width: 100%; justify-content: space-between; border-top: 1px solid #1e293b; padding-top: 15px; }
}
</style>