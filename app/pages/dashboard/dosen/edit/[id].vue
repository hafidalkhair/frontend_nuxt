<script setup>
import { ref, onMounted } from 'vue';
import { useRouter, useRoute } from 'vue-router';

const router = useRouter();
const route = useRoute();

const prodiList = ref([]);
const loading = ref(false);
const dataLoading = ref(true); // State untuk loading saat ambil data awal
const form = ref({ 
  nama_lengkap:'', nidn:'', nip:'', tmt:'', 
  program_studi_id:'', jabatan_fungsional:'tenaga pengajar', 
  status:'aktif', jenis_kelamin:'1' 
});

// --- 1. Ambil Data Prodi & Dosen saat halaman dibuka ---
onMounted(async () => {
  const token = localStorage.getItem('token');
  if(!token) return router.push('/login');

  try {
    // Fetch data secara parallel agar lebih cepat
    const [resProdi, resDosen] = await Promise.all([
      fetch('https://hafid.copium.id/api/program-studis', { headers: { 'Authorization': `Bearer ${token}` } }),
      fetch(`https://hafid.copium.id/api/dosens/${route.params.id}`, { headers: { 'Authorization': `Bearer ${token}` } })
    ]);

    const dP = await resProdi.json();
    const dD = await resDosen.json();
    
    prodiList.value = dP.data || dP;
    
    // Isi form dengan data dosen yang didapat
    const d = dD.data || dD;
    form.value = { 
      ...d, 
      nidn: d.nidn || '', // Pastikan tidak null agar input tidak error
      nip: d.nip || '',
      tmt: d.tmt || '',
      program_studi_id: d.program_studi_id || '', // Handle jika prodi null
      jenis_kelamin: d.jenis_kelamin ? '1' : '0'
    };

  } catch (error) {
    alert('Gagal mengambil data dosen.');
    router.push('/dashboard/dosen');
  } finally {
    dataLoading.value = false;
  }
});

// --- 2. Fungsi Update Data ---
const update = async () => {
  loading.value = true;
  const token = localStorage.getItem('token');
  
  // Ubah string kosong kembali menjadi null untuk dikirim ke API
  const payload = { ...form.value };
  if(!payload.nidn) payload.nidn = null;
  if(!payload.nip) payload.nip = null;
  if(!payload.tmt) payload.tmt = null;

  try {
     const res = await fetch(`https://hafid.copium.id/api/dosens/${route.params.id}`, {
      method: 'PUT', // Gunakan method PUT untuk update
      headers: { 'Content-Type': 'application/json', 'Authorization': `Bearer ${token}` },
      body: JSON.stringify(payload)
    });

    if(res.ok) {
      router.push('/dashboard/dosen');
    } else {
      const data = await res.json();
      alert(data.message || 'Gagal update data.');
    }
  } catch (error) {
    alert('Terjadi kesalahan koneksi.');
  } finally { 
    loading.value = false; 
  }
};
</script>

<template>
  <div class="dark-container">
    <div class="form-card">
      <div class="form-header">
        <h2>Edit Lecturer</h2>
        <p>Perbarui data dosen pengajar.</p>
      </div>

      <div v-if="dataLoading" class="loader-text">
        Loading Data...
      </div>

      <form v-else @submit.prevent="update" class="grid-form">
        <div class="col">
          <div class="input-group">
            <label>Nama Lengkap</label>
            <input v-model="form.nama_lengkap" class="dark-input" placeholder="Nama & Gelar" required>
          </div>
          
          <div class="row-inputs">
             <div class="input-group">
                <label>NIDN</label>
                <input v-model="form.nidn" class="dark-input" placeholder="-">
             </div>
             <div class="input-group">
                <label>NIP</label>
                <input v-model="form.nip" class="dark-input" placeholder="-">
             </div>
          </div>

          <div class="input-group">
            <label>Program Studi</label>
            <select v-model="form.program_studi_id" class="dark-input">
              <option value="">- Pilih Prodi -</option>
              <option v-for="p in prodiList" :value="p.id">{{ p.program_studi }}</option>
            </select>
          </div>
        </div>

        <div class="col">
          <div class="input-group">
             <label>Jabatan Fungsional</label>
             <select v-model="form.jabatan_fungsional" class="dark-input">
                <option value="tenaga pengajar">Tenaga Pengajar</option>
                <option value="asisten ahli">Asisten Ahli</option>
                <option value="lektor">Lektor</option>
                <option value="guru besar">Guru Besar</option>
             </select>
          </div>
          
          <div class="row-inputs">
            <div class="input-group">
               <label>Status</label>
               <select v-model="form.status" class="dark-input">
                  <option value="aktif">Aktif</option>
                  <option value="cuti">Cuti</option>
                  <option value="tugas belajar">Tugas Belajar</option>
               </select>
            </div>
            <div class="input-group">
                <label>TMT</label>
                <input v-model="form.tmt" type="date" class="dark-input">
            </div>
          </div>
        </div>

        <div class="action-row">
          <button type="button" @click="router.back()" class="btn-cancel">Batal</button>
          <button type="submit" class="btn-save warning" :disabled="loading">
            {{ loading ? 'Menyimpan...' : 'Update Data' }}
          </button>
        </div>
      </form>
    </div>
  </div>
</template>

<style scoped>
/* --- STYLES DASAR (Sama dengan Create Dosen) --- */
.dark-container { min-height: 100vh; background: #020617; display: flex; justify-content: center; align-items: center; padding: 40px; font-family: 'Plus Jakarta Sans', sans-serif; }
.form-card { width: 100%; max-width: 900px; background: #0f172a; border: 1px solid #1e293b; border-radius: 24px; padding: 40px; box-shadow: 0 25px 50px -12px rgba(0,0,0,0.5); }

.form-header { margin-bottom: 30px; border-bottom: 1px solid #1e293b; padding-bottom: 20px; }
.form-header h2 { color: #f8fafc; margin: 0; font-size: 24px; font-weight: 700; }
.form-header p { color: #64748b; margin: 5px 0 0 0; font-size: 14px; }

.grid-form { display: grid; grid-template-columns: 1fr 1fr; gap: 40px; }
.col { display: flex; flex-direction: column; gap: 20px; }

.input-group label { display: block; color: #94a3b8; font-size: 13px; font-weight: 600; margin-bottom: 8px; }
.dark-input { width: 100%; background: #1e293b; border: 1px solid #334155; color: #f8fafc; padding: 12px 16px; border-radius: 10px; font-size: 14px; outline: none; transition: 0.2s; box-sizing: border-box; }
.dark-input:focus { border-color: #6366f1; box-shadow: 0 0 0 3px rgba(99, 102, 241, 0.2); }

.row-inputs { display: flex; gap: 15px; }
.row-inputs .input-group { flex: 1; }

.action-row { grid-column: span 2; display: flex; justify-content: flex-end; gap: 15px; border-top: 1px solid #1e293b; padding-top: 30px; margin-top: 10px; }
.btn-cancel { background: transparent; color: #94a3b8; border: 1px solid #334155; padding: 12px 24px; border-radius: 10px; cursor: pointer; font-weight: 600; }

/* Tombol Save Default (Biru) */
.btn-save { background: #6366f1; color: white; border: none; padding: 12px 30px; border-radius: 10px; cursor: pointer; font-weight: 700; transition: 0.2s; }

/* Tombol Warning untuk Edit (Oranye) */
.btn-save.warning { background: #f59e0b; }
.btn-save.warning:hover { background: #d97706; box-shadow: 0 0 15px rgba(245, 158, 11, 0.4); }

.loader-text { text-align: center; padding: 40px; color: #64748b; }

/* --- 🔥 MOBILE RESPONSIVE FIX (Penting!) --- */
@media (max-width: 768px) {
  .dark-container { align-items: flex-start; padding: 20px 15px; }
  .form-card { padding: 25px; border-radius: 16px; }
  .grid-form { grid-template-columns: 1fr; gap: 20px; } /* 1 Kolom */
  .row-inputs { flex-direction: column; gap: 20px; } /* Stack Input */
  .action-row { grid-column: span 1; flex-direction: column-reverse; gap: 15px; }
  .btn-save, .btn-cancel { width: 100%; padding: 14px; font-size: 16px; }
}
</style>