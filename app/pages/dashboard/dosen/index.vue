<script setup>
import { ref, onMounted } from 'vue';
import { useRouter } from 'vue-router';
const router = useRouter();
const dosens = ref([]);
const showModal = ref(false);
const deleteId = ref(null);

onMounted(() => {
  const token = localStorage.getItem('token');
  fetch('https://hafid.copium.id/api/dosens', { headers: { 'Authorization': `Bearer ${token}` } })
    .then(res => res.json()).then(data => dosens.value = data.data || data);
});

const confirmDelete = (id) => { deleteId.value = id; showModal.value = true; };
const executeDelete = async () => {
  const token = localStorage.getItem('token');
  await fetch(`https://hafid.copium.id/api/dosens/${deleteId.value}`, { method: 'DELETE', headers: { 'Authorization': `Bearer ${token}` } });
  dosens.value = dosens.value.filter(d => d.id !== deleteId.value);
  showModal.value = false;
};
</script>

<template>
  <div class="container">
    <div class="glass-panel">
      <div class="header">
        <h1>Dashboard Admin</h1>
        <button @click="router.push('/login')" class="btn-logout">Logout</button>
      </div>
      
      <div class="nav-bar">
        <NuxtLink to="/dashboard"><button class="nav-btn">Mahasiswa</button></NuxtLink>
        <button class="nav-btn active">Dosen</button>
        <NuxtLink to="/dashboard/dosen/create" style="margin-left: auto;"><button class="btn-add">+ Dosen</button></NuxtLink>
      </div>

      <div class="list">
        <div v-for="d in dosens" :key="d.id" class="item">
          <div>
            <strong>{{ d.nama_lengkap }}</strong>
            <div style="font-size: 13px; color: #666;">
              {{ d.nidn || d.nip || '-' }} | <span style="text-transform:uppercase;">{{ d.jabatan_fungsional }}</span>
            </div>
          </div>
          <div class="actions">
            <NuxtLink :to="`/dashboard/dosen/edit/${d.id}`"><button class="btn-edit">Edit</button></NuxtLink>
            <button @click="confirmDelete(d.id)" class="btn-del">Hapus</button>
          </div>
        </div>
      </div>
    </div>
    
    <div v-if="showModal" class="modal">
      <div class="modal-box">
        <h3>Hapus Dosen?</h3>
        <div class="modal-act">
          <button @click="showModal=false">Batal</button>
          <button @click="executeDelete" class="btn-del">Hapus</button>
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped>
/* Gunakan style yang SAMA PERSIS dengan dashboard/index.vue */
.container { min-height: 100vh; padding: 40px; background: linear-gradient(-45deg, #ee7752, #e73c7e, #23a6d5, #23d5ab); background-size: 400% 400%; animation: gradientBG 15s infinite; font-family: sans-serif; }
.glass-panel { background: rgba(255,255,255,0.9); padding: 40px; border-radius: 20px; max-width: 800px; margin: 0 auto; }
.header { display: flex; justify-content: space-between; align-items: center; margin-bottom: 20px; }
.nav-bar { display: flex; gap: 10px; margin-bottom: 20px; }
.nav-btn { padding: 8px 15px; border: none; background: #eee; border-radius: 5px; cursor: pointer; }
.nav-btn.active { background: #2563eb; color: white; }
.btn-add { background: #10b981; color: white; border: none; padding: 8px 15px; border-radius: 5px; cursor: pointer; }
.btn-logout { background: #ef4444; color: white; border: none; padding: 8px 15px; border-radius: 5px; cursor: pointer; }
.item { display: flex; justify-content: space-between; padding: 15px; border-bottom: 1px solid #eee; }
.actions { display: flex; gap: 5px; }
.btn-edit { background: #f59e0b; color: white; border: none; padding: 5px 10px; border-radius: 4px; }
.btn-del { background: #ef4444; color: white; border: none; padding: 5px 10px; border-radius: 4px; cursor: pointer; }
.modal { position: fixed; top:0; left:0; width:100%; height:100%; background: rgba(0,0,0,0.5); display: flex; justify-content: center; align-items: center; }
.modal-box { background: white; padding: 20px; border-radius: 10px; text-align: center; width: 300px; }
.modal-act { display: flex; justify-content: center; gap: 10px; margin-top: 15px; }
@keyframes gradientBG { 0% { background-position: 0% 50%; } 50% { background-position: 100% 50%; } 100% { background-position: 0% 50%; } }
</style>