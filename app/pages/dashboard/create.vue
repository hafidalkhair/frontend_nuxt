<script setup>
import { ref, onMounted } from 'vue';
import { useRouter } from 'vue-router';
const router = useRouter();
const prodiList = ref([]);
const form = ref({ nim:'', nama:'', program_studi_id:'', email:'', nomor_hp:'', jenis_kelamin:'1', tempat_lahir:'', tanggal_lahir:'' });

onMounted(async () => {
  const token = localStorage.getItem('token');
  const res = await fetch('https://hafid.copium.id/api/program-studis', { headers: { 'Authorization': `Bearer ${token}` } });
  const data = await res.json();
  prodiList.value = data.data || data;
});

const submit = async () => {
  const token = localStorage.getItem('token');
  const res = await fetch('https://hafid.copium.id/api/mahasiswa', {
    method: 'POST', headers: { 'Content-Type': 'application/json', 'Authorization': `Bearer ${token}` },
    body: JSON.stringify(form.value)
  });
  if(res.ok) router.push('/dashboard');
};
</script>

<template>
  <div class="container">
    <div class="glass-panel">
      <h2>Tambah Mahasiswa</h2>
      <hr style="margin: 20px 0; border: 0; border-top: 1px solid #ddd;">
      <form @submit.prevent="submit" class="grid-form">
        <div class="col">
          <label>NIM</label><input v-model="form.nim" class="input" required>
          <label>Nama</label><input v-model="form.nama" class="input" required>
          <label>Prodi</label>
          <select v-model="form.program_studi_id" class="input" required>
            <option v-for="p in prodiList" :value="p.id">{{ p.program_studi }}</option>
          </select>
          <label>Gender</label>
          <select v-model="form.jenis_kelamin" class="input">
            <option value="1">Laki-laki</option><option value="0">Perempuan</option>
          </select>
        </div>
        <div class="col">
          <label>Email</label><input v-model="form.email" class="input" type="email" required>
          <label>HP</label><input v-model="form.nomor_hp" class="input" required>
          <label>Tempat Lahir</label><input v-model="form.tempat_lahir" class="input" required>
          <label>Tgl Lahir</label><input v-model="form.tanggal_lahir" type="date" class="input" required>
        </div>
        <div class="full-width">
          <button type="submit" class="btn-save">Simpan Data</button>
          <button type="button" @click="router.back()" class="btn-cancel">Batal</button>
        </div>
      </form>
    </div>
  </div>
</template>

<style scoped>
/* Gunakan style container & glass-panel yang sama dengan dashboard index */
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