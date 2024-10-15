<template>
  <div class="container-fluid">
    <div class="row">
      <div class="col-12 bg-danger-subtle">
        <div class="d-flex justify-content-center pt-5 pb-3">
          <h1>Tambah Siswa</h1>
        </div>

        <!-- Form to Add New Siswa -->
        <form @submit.prevent="addSiswa" class="p-4">
          <div class="mb-3">
            <label for="nis" class="form-label">NIS</label>
            <input v-model="nis" type="text" class="form-control" id="nis" placeholder="Masukkan NIS" required>
          </div>

          <div class="mb-3">
            <label for="nama" class="form-label">Nama</label>
            <input v-model="nama" type="text" class="form-control" id="nama" placeholder="Masukkan Nama" required>
          </div>

          <div class="mb-3">
            <label for="jenisKelamin" class="form-label">Jenis Kelamin</label>
            <select v-model="jnskel" id="jenisKelamin" class="form-control" required>
              <option value="">Pilih Jenis Kelamin</option>
              <option value="Laki Laki">Laki-laki</option>
              <option value="Perempuan">Perempuan</option>
            </select>
          </div>

          <div class="mb-3">
            <label for="kelas" class="form-label">Kelas</label>
            <input v-model="kelas" type="text" class="form-control" id="kelas" placeholder="Masukkan Kelas" required>
          </div>

          <div class="mb-3">
            <label for="jurusan" class="form-label">Jurusan</label>
            <select v-model="jurusanId" id="jurusan" class="form-control" required>
              <option v-for="jur in jurusan" :key="jur.id" :value="jur.id">{{ jur.nama }}</option>
            </select>
          </div>

          <div class="mb-3">
            <label for="noKelas" class="form-label">No Kelas</label>
            <input v-model="noKelas" type="text" class="form-control" id="noKelas" placeholder="Masukkan No Kelas"
              required>
          </div>

          <button type="submit" class="btn btn-primary">Tambah Siswa</button>
        </form>
        <div class="pb-3">
          <nuxt-link to="/admin/DataSiswa/">
            <button class="btn btn-danger">Kembali</button>
          </nuxt-link>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
definePageMeta({
  layout: 'admin'
})

const supabase = useSupabaseClient()
const nis = ref('')
const nama = ref('')
const kelas = ref('')
const jurusanId = ref('')
const jurusan = ref([])
const noKelas = ref('')
const jnskel = ref('')
const siswaList = ref([]) // Array to hold the list of students

const getJurusan = async () => {
  let { data, error } = await supabase
    .from('jurusan')
    .select('*')

  if (data) {
    jurusan.value = data;
  } else {
    console.error(error);
  }
}

const addSiswa = async () => {
  const { data, error } = await supabase
    .from('siswa')
    .insert([{
      nis: nis.value,
      nama: nama.value,
      tingkat: kelas.value,
      jurusan: jurusanId.value,
      kelas: noKelas.value,
      jnskel: jnskel.value,
    }]);

  if (error) {
    console.error("Error adding siswa:", error);
  } else {
    alert('Siswa berhasil ditambahkan');

    // Add the new student to the local array
    siswaList.value.push({
      nis: nis.value,
      nama: nama.value,
      tingkat: kelas.value,
      jurusan: jurusanId.value,
      kelas: noKelas.value,
      jnskel: jnskel.value,
    });

    // Reset form fields
    nis.value = '';
    nama.value = '';
    kelas.value = '';
    jurusanId.value = '';
    noKelas.value = '';
    jnskel.value = '';
  }
}

onMounted(() => {
  getJurusan();
});
</script>

<style scoped>
.row {
  width: auto;
  height: 100vh;
}

.nav-link {
  width: 100%;
  padding: 10px;
  display: block;
  transition: background-color 0.3s ease, color 0.3s ease;
  border-radius: 5px;
}

.nav-link:hover {
  background-color: #ff0000;
  color: #007bff;
  font-weight: bold;
  cursor: pointer;
}

form {
  max-width: 600px;
  margin: 0 auto;
}

.form-label {
  font-weight: bold;
}

.mb-3 {
  margin-bottom: 1.5rem;
}

.btn-primary {
  background-color: #0d6efd;
  border-color: #0d6efd;
}
</style>
