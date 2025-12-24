<script setup>
import { ref, onMounted } from 'vue';
import { useRouter } from 'vue-router';

const router = useRouter();
const prodiList = ref([]);
const loading = ref(false);
// Inisialisasi errors sebagai object kosong agar tidak undefined
const errors = ref({}); 

const form = ref({ 
  nim:'', 
  nama:'', 
  program_studi_id:'', 
  email:'', 
  nomor_hp:'', 
  jenis_kelamin:'1', 
  tempat_lahir:'', 
  tanggal_lahir:'' 
});

onMounted(async () => {
  const token = localStorage.getItem('token');
  try {
    const res = await fetch('https://hafid.copium.id/api/program-studis', { 
      headers: { 'Authorization': `Bearer ${token}` } 
    });
    const data = await res.json();
    prodiList.value = data.data || data;
  } catch (err) {
    console.error("Gagal load prodi", err);
  }
});

const submit = async () => {
  loading.value = true;
  errors.value = {}; // Reset error sebelum kirim
  const token = localStorage.getItem('token');
  
  try {
    const res = await fetch('https://hafid.copium.id/api/mahasiswa', {
      method: 'POST', 
      headers: { 
        'Content-Type': 'application/json', 
        'Authorization': `Bearer ${token}`,
        'Accept': 'application/json' // WAJIB: Agar Laravel balas JSON, bukan HTML
      },
      body: JSON.stringify(form.value)
    });

    const responseData = await res.json();

    if (!res.ok) {
      // JIKA VALIDASI GAGAL (422)
      if (res.status === 422) {
        console.log("Respon Error dari Server:", responseData); // Cek Console untuk debugging
        
        // Perbaikan: Gunakan '|| {}' agar errors.value tidak pernah undefined
        errors.value = responseData.errors || {}; 
        
        alert("Gagal Validasi: " + (responseData.message || "Periksa inputan anda."));
      } else {
        alert("Terjadi kesalahan sistem: " + (responseData.message || res.statusText));
      }
    } else {
      // BERHASIL
      alert("Berhasil menambahkan data!");
      router.push('/dashboard');
    }
  } catch (err) {
    console.error(err);
    alert("Koneksi Error. Pastikan internet lancar.");
  } finally { 
    loading.value = false; 
  }
};
</script>

<template>
  <div class="dark-container">
    <div class="form-card">
      <div class="form-header">
        <h2>New Student</h2>
        <p>Tambahkan data mahasiswa baru.</p>
      </div>

      <form @submit.prevent="submit" class="grid-form">
        <div class="col">
          <div class="input-wrap">
            <label>NIM</label>
            <input v-model="form.nim" class="dark-input" required placeholder="Contoh: 2023001">
            <small v-if="errors?.nim" class="error-text">{{ errors.nim[0] }}</small>
          </div>

          <div class="input-wrap">
            <label>Nama</label>
            <input v-model="form.nama" class="dark-input" required>
            <small v-if="errors?.nama" class="error-text">{{ errors.nama[0] }}</small>
          </div>

          <div class="input-wrap">
            <label>Prodi</label>
            <select v-model="form.program_studi_id" class="dark-input" required>
              <option value="" disabled>Pilih Prodi</option>
              <option v-for="p in prodiList" :value="p.id">{{ p.program_studi }}</option>
            </select>
            <small v-if="errors?.program_studi_id" class="error-text">{{ errors.program_studi_id[0] }}</small>
          </div>

          <div class="input-wrap">
            <label>Jenis Kelamin</label>
            <select v-model="form.jenis_kelamin" class="dark-input">
               <option value="1">Laki-laki</option>
               <option value="0">Perempuan</option>
            </select>
             <small v-if="errors?.jenis_kelamin" class="error-text">{{ errors.jenis_kelamin[0] }}</small>
          </div>
        </div>

        <div class="col">
          <div class="input-wrap">
            <label>Email</label>
            <input v-model="form.email" type="email" class="dark-input" required>
            <small v-if="errors?.email" class="error-text">{{ errors.email[0] }}</small>
          </div>

          <div class="input-wrap">
            <label>HP</label>
            <input v-model="form.nomor_hp" class="dark-input" required>
            <small v-if="errors?.nomor_hp" class="error-text">{{ errors.nomor_hp[0] }}</small>
          </div>

          <div class="row-inputs">
             <div class="input-wrap">
                <label>Tgl Lahir</label>
                <input v-model="form.tanggal_lahir" type="date" class="dark-input" required>
                <small v-if="errors?.tanggal_lahir" class="error-text">{{ errors.tanggal_lahir[0] }}</small>
             </div>
             <div class="input-wrap">
                <label>Tempat</label>
                <input v-model="form.tempat_lahir" class="dark-input" required>
             </div>
          </div>
        </div>

        <div class="action-row">
          <button type="button" @click="router.back()" class="btn-cancel">Cancel</button>
          <button type="submit" class="btn-save" :disabled="loading">
            {{ loading ? 'Saving...' : 'Save Data' }}
          </button>
        </div>
      </form>
    </div>
  </div>
</template>

<style scoped>
.error-text { color: #ef4444; font-size: 12px; margin-top: 4px; display: block; font-weight: bold; }

.dark-container { min-height: 100vh; background: #020617; display: flex; justify-content: center; align-items: center; padding: 40px; }
.form-card { width: 100%; max-width: 900px; background: #0f172a; border: 1px solid #1e293b; border-radius: 24px; padding: 40px; box-shadow: 0 25px 50px -12px rgba(0,0,0,0.5); }
.form-header h2 { color: #f8fafc; margin: 0; font-size: 24px; font-weight: 700; }
.form-header p { color: #64748b; margin: 5px 0 20px 0; font-size: 14px; }
.grid-form { display: grid; grid-template-columns: 1fr 1fr; gap: 40px; }
.col { display: flex; flex-direction: column; gap: 20px; }
.input-wrap label { display: block; color: #94a3b8; font-size: 13px; font-weight: 600; margin-bottom: 8px; }
.dark-input { width: 100%; background: #1e293b; border: 1px solid #334155; color: #f8fafc; padding: 12px 16px; border-radius: 10px; font-size: 14px; outline: none; transition: 0.2s; box-sizing: border-box; }
.dark-input:focus { border-color: #6366f1; box-shadow: 0 0 0 3px rgba(99, 102, 241, 0.2); }
.row-inputs { display: flex; gap: 15px; } .row-inputs .input-wrap { flex: 1; }
.action-row { grid-column: span 2; display: flex; justify-content: flex-end; gap: 15px; border-top: 1px solid #1e293b; padding-top: 30px; margin-top: 10px; }
.btn-save { background: #6366f1; color: white; border: none; padding: 12px 30px; border-radius: 10px; cursor: pointer; font-weight: 700; }
.btn-cancel { background: transparent; color: #94a3b8; border: 1px solid #334155; padding: 12px 24px; border-radius: 10px; cursor: pointer; font-weight: 600; }

@media (max-width: 768px) {
  .dark-container { padding: 20px 10px; align-items: flex-start; }
  .form-card { padding: 25px; }
  .grid-form { grid-template-columns: 1fr; gap: 20px; }
  .row-inputs { flex-direction: column; gap: 20px; }
  .action-row { grid-column: span 1; flex-direction: column-reverse; }
  .btn-save, .btn-cancel { width: 100%; padding: 15px; }
}
</style>