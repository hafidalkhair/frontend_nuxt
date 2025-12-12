<script setup>
import { ref, onMounted } from 'vue';
import { useRouter, useRoute } from 'vue-router';
const router = useRouter();
const route = useRoute();
const prodiList = ref([]);
const loading = ref(false);
const form = ref({}); // Init kosong

onMounted(async () => {
  const token = localStorage.getItem('token');
  // Fetch Parallel
  const [resProdi, resMhs] = await Promise.all([
     fetch('https://hafid.copium.id/api/program-studis', { headers: { 'Authorization': `Bearer ${token}` } }),
     fetch(`https://hafid.copium.id/api/mahasiswa/${route.params.id}`, { headers: { 'Authorization': `Bearer ${token}` } })
  ]);
  const dP = await resProdi.json();
  const dM = await resMhs.json();
  
  prodiList.value = dP.data || dP;
  const m = dM.data || dM;
  form.value = { ...m, jenis_kelamin: m.jenis_kelamin ? '1' : '0' }; // Map data
});

const update = async () => {
  loading.value = true;
  const token = localStorage.getItem('token');
  try {
     const res = await fetch(`https://hafid.copium.id/api/mahasiswa/${route.params.id}`, {
      method: 'PUT', headers: { 'Content-Type': 'application/json', 'Authorization': `Bearer ${token}` },
      body: JSON.stringify(form.value)
    });
    if(res.ok) router.push('/dashboard');
  } finally { loading.value = false; }
};
</script>

<template>
  <div class="dark-container">
    <div class="form-card">
      <div class="form-header">
        <h2>Edit Data</h2>
        <p>Perbarui data mahasiswa yang terdaftar.</p>
      </div>
      <form @submit.prevent="update" class="grid-form">
         <div class="col">
          <div class="input-group"><label>NIM</label><input v-model="form.nim" class="dark-input" required></div>
          <div class="input-group"><label>Nama</label><input v-model="form.nama" class="dark-input" required></div>
          <div class="input-group"><label>Prodi</label>
            <select v-model="form.program_studi_id" class="dark-input" required>
              <option v-for="p in prodiList" :value="p.id">{{ p.program_studi }}</option>
            </select>
          </div>
          <div class="input-group"><label>Gender</label>
            <div class="radio-group">
              <label class="radio-btn"><input type="radio" v-model="form.jenis_kelamin" value="1"><span>Laki</span></label>
              <label class="radio-btn"><input type="radio" v-model="form.jenis_kelamin" value="0"><span>Perempuan</span></label>
            </div>
          </div>
        </div>

        <div class="col">
          <div class="input-group"><label>Email</label><input v-model="form.email" class="dark-input" required></div>
          <div class="input-group"><label>HP</label><input v-model="form.nomor_hp" class="dark-input" required></div>
          <div class="row-inputs">
             <div class="input-group"><label>Tempat Lahir</label><input v-model="form.tempat_lahir" class="dark-input" required></div>
             <div class="input-group"><label>Tgl Lahir</label><input v-model="form.tanggal_lahir" type="date" class="dark-input" required></div>
          </div>
        </div>

        <div class="action-row">
          <button type="button" @click="router.back()" class="btn-cancel">Cancel</button>
          <button type="submit" class="btn-save warning">Update Changes</button>
        </div>
      </form>
    </div>
  </div>
</template>

<style scoped>
/* Copy Style dari Create.vue, tambahkan ini: */
.btn-save.warning { background: #f59e0b; }
.btn-save.warning:hover { background: #d97706; box-shadow: 0 0 15px rgba(245, 158, 11, 0.4); }

/* --- PASTE STYLE DARI CREATE.VUE DI SINI (dark-container, form-card, dark-input, dll) --- */
.dark-container { min-height: 100vh; background: #020617; display: flex; justify-content: center; align-items: center; padding: 40px; }
.form-card { width: 100%; max-width: 900px; background: #0f172a; border: 1px solid #1e293b; border-radius: 24px; padding: 40px; box-shadow: 0 25px 50px -12px rgba(0,0,0,0.5); }
.form-header h2 { color: #f8fafc; margin: 0; font-size: 24px; font-weight: 700; }
.form-header p { color: #64748b; margin: 5px 0 30px 0; font-size: 14px; }
.grid-form { display: grid; grid-template-columns: 1fr 1fr; gap: 40px; }
.col { display: flex; flex-direction: column; gap: 20px; }
.input-group label { display: block; color: #94a3b8; font-size: 13px; font-weight: 600; margin-bottom: 8px; }
.dark-input { width: 100%; background: #1e293b; border: 1px solid #334155; color: #f8fafc; padding: 12px 16px; border-radius: 10px; font-size: 14px; outline: none; transition: 0.2s; box-sizing: border-box; }
.dark-input:focus { border-color: #6366f1; box-shadow: 0 0 0 3px rgba(99, 102, 241, 0.2); }
.radio-group { display: flex; gap: 15px; background: #1e293b; padding: 5px; border-radius: 10px; border: 1px solid #334155; }
.radio-btn { flex: 1; position: relative; cursor: pointer; }
.radio-btn input { position: absolute; opacity: 0; }
.radio-btn span { display: block; text-align: center; padding: 10px; border-radius: 8px; color: #94a3b8; font-size: 13px; font-weight: 600; transition: 0.2s; }
.radio-btn input:checked + span { background: #334155; color: #fff; }
.row-inputs { display: flex; gap: 15px; }
.row-inputs .input-group { flex: 1; }
.action-row { grid-column: span 2; display: flex; justify-content: flex-end; gap: 15px; border-top: 1px solid #1e293b; padding-top: 30px; margin-top: 10px; }
.btn-cancel { background: transparent; color: #94a3b8; border: 1px solid #334155; padding: 12px 24px; border-radius: 10px; cursor: pointer; font-weight: 600; }
.btn-cancel:hover { background: #1e293b; color: #fff; }
.btn-save { background: #6366f1; color: white; border: none; padding: 12px 30px; border-radius: 10px; cursor: pointer; font-weight: 700; transition: 0.2s; }
</style>