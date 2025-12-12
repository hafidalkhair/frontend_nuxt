<script setup>
import { ref, onMounted } from 'vue';
import { useRouter } from 'vue-router';
const router = useRouter();
const prodiList = ref([]);
const form = ref({ nama_lengkap:'', nidn:'', nip:'', tmt:'', program_studi_id:'', jabatan_fungsional:'tenaga pengajar', status:'aktif', jenis_kelamin:'1' });

onMounted(async () => {
  const token = localStorage.getItem('token');
  const res = await fetch('https://hafid.copium.id/api/program-studis', { headers: { 'Authorization': `Bearer ${token}` } });
  const data = await res.json();
  prodiList.value = data.data || data;
});

const submit = async () => {
  const token = localStorage.getItem('token');
  // Handle empty strings
  const payload = { ...form.value };
  if(!payload.nidn) payload.nidn = null;
  if(!payload.nip) payload.nip = null;
  
  const res = await fetch('https://hafid.copium.id/api/dosens', {
    method: 'POST', headers: { 'Content-Type': 'application/json', 'Authorization': `Bearer ${token}` },
    body: JSON.stringify(payload)
  });
  if(res.ok) router.push('/dashboard/dosen');
};
</script>

<template>
  <div class="container">
    <div class="glass-panel">
      <h2>Tambah Dosen</h2>
      <hr style="margin: 20px 0; border: 0; border-top: 1px solid #ddd;">
      <form @submit.prevent="submit" class="grid-form">
        <div class="col">
          <label>Nama Lengkap</label><input v-model="form.nama_lengkap" class="input" required>
          <div style="display:flex; gap:10px;">
            <div style="flex:1"><label>NIDN</label><input v-model="form.nidn" class="input"></div>
            <div style="flex:1"><label>NIP</label><input v-model="form.nip" class="input"></div>
          </div>
          <label>Prodi</label>
          <select v-model="form.program_studi_id" class="input">
            <option value="">- Opsional -</option>
            <option v-for="p in prodiList" :value="p.id">{{ p.program_studi }}</option>
          </select>
        </div>
        <div class="col">
          <label>Jabatan</label>
          <select v-model="form.jabatan_fungsional" class="input">
             <option value="tenaga pengajar">Tenaga Pengajar</option>
             <option value="asisten ahli">Asisten Ahli</option>
             <option value="lektor">Lektor</option>
             <option value="lektor kepala">Lektor Kepala</option>
             <option value="guru besar">Guru Besar</option>
          </select>
          <label>Status</label>
          <select v-model="form.status" class="input">
             <option value="aktif">Aktif</option>
             <option value="cuti">Cuti</option>
             <option value="tugas belajar">Tugas Belajar</option>
          </select>
          <label>TMT</label><input v-model="form.tmt" type="date" class="input">
        </div>
        <div class="full-width">
          <button type="submit" class="btn-save">Simpan</button>
          <button type="button" @click="router.back()" class="btn-cancel">Batal</button>
        </div>
      </form>
    </div>
  </div>
</template>

<style scoped>
/* Copy Style dari Create Mahasiswa (Sama Persis) */
.container { min-height: 100vh; padding: 40px; background: linear-gradient(-45deg, #ee7752, #e73c7e, #23a6d5, #23d5ab); background-size: 400% 400%; animation: gradientBG 15s infinite; display: flex; justify-content: center; }
.glass-panel { background: rgba(255,255,255,0.95); padding: 40px; border-radius: 20px; width: 100%; max-width: 800px; height: fit-content; }
.grid-form { display: grid; grid-template-columns: 1fr 1fr; gap: 30px; }
.col { display: flex; flex-direction: column; gap: 10px; }
.input { padding: 10px; border-radius: 8px; border: 1px solid #ccc; width: 100%; box-sizing: border-box; }
.full-width { grid-column: span 2; display: flex; gap: 10px; justify-content: flex-end; margin-top: 20px; }
.btn-save { padding: 10px 20px; background: #2563eb; color: white; border: none; border-radius: 8px; cursor: pointer; }
.btn-cancel { padding: 10px 20px; background: #ccc; border: none; border-radius: 8px; cursor: pointer; }
@keyframes gradientBG { 0% { background-position: 0% 50%; } 50% { background-position: 100% 50%; } 100% { background-position: 0% 50%; } }
</style>