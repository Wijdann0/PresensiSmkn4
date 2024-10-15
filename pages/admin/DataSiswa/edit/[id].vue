<template>
  <div class="container-fluid">
    <div class="row">
      <div class="col-12 bg-danger-subtle">
        <div class="d-flex justify-content-center pt-5 pb-3">
          <h1>Edit Siswa</h1>
        </div>

        <!-- Form to Edit Existing Siswa -->
        <form @submit.prevent="updateSiswa" class="p-4">
          <div class="mb-3">
            <label for="nis" class="form-label">NIS</label>
            <input v-model="siswa.nis" type="number" class="form-control" id="nis" placeholder="Masukkan NIS" required>
          </div>

          <div class="mb-3">
            <label for="nama" class="form-label">Nama</label>
            <input v-model="siswa.nama" type="text" class="form-control" id="nama" placeholder="Masukkan Nama" required>
          </div>

          <div class="mb-3">
            <label for="jenisKelamin" class="form-label">Jenis Kelamin</label>
            <select v-model="siswa.jnskel" id="jenisKelamin" class="form-control" required>
              <option value="">Pilih Jenis Kelamin</option>
              <option value="Laki Laki">Laki-laki</option>
              <option value="Perempuan">Perempuan</option>
            </select>
          </div>

          <div class="mb-3">
            <label for="kelas" class="form-label">Kelas</label>
            <input v-model="siswa.tingkat" type="text" class="form-control" id="kelas" placeholder="Masukkan Kelas" required>
          </div>

          <div class="mb-3">
            <label for="jurusan" class="form-label">Jurusan</label>
            <select v-model="siswa.jurusan" id="jurusan" class="form-control" required>
              <option v-for="jur in jurusanList" :key="jur.id" :value="jur.id">{{ jur.nama }}</option>
            </select>
          </div>

          <div class="mb-3">
            <label for="noKelas" class="form-label">No Kelas</label>
            <input v-model="siswa.kelas" type="text" class="form-control" id="noKelas" placeholder="Masukkan Kelas" required>
          </div>

          <button type="submit" class="btn btn-primary">Update Siswa</button>
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
import { ref, onMounted } from 'vue'
import { useRoute, useRouter } from 'vue-router'

const supabase = useSupabaseClient()
const route = useRoute()
const router = useRouter()

// Data siswa yang akan diupdate
const siswa = ref({
  nis: '',
  nama: '',
  jnskel: '',
  tingkat: '',
  jurusan: '',
  kelas: ''
})

// List jurusan untuk pilihan di form
const jurusanList = ref([])

// Mendapatkan data siswa berdasarkan ID
onMounted(async () => {
  const { id } = route.params;

  const { data: siswaData, error: siswaError } = await supabase
    .from('siswa')
    .select('*')
    .eq('id', id)
    .single();

  if (siswaData) {
    siswa.value = { ...siswaData };
    console.log('Siswa data fetched:', siswa.value); // Log untuk memeriksa data siswa
  } else {
    console.error('Error fetching siswa:', siswaError ? siswaError.message : 'Unknown error');
  }

  // Mendapatkan list jurusan
  const { data: jurusanData, error: jurusanError } = await supabase
    .from('jurusan')
    .select('id, nama');

  if (jurusanData) {
    jurusanList.value = jurusanData;
  } else {
    console.error('Error fetching jurusan:', jurusanError ? jurusanError.message : 'Unknown error');
  }
});

const updateSiswa = async () => {
  const { id } = route.params;

  // Validasi input
  if (!siswa.value.nis || !siswa.value.nama || !siswa.value.jnskel || !siswa.value.tingkat || !siswa.value.jurusan || !siswa.value.kelas) {
    alert('Semua field harus diisi!');
    return;
  }

  console.log('Siswa before update:', siswa.value); // Log sebelum update

  try {
    const { error } = await supabase
      .from('siswa')
      .update({
        nis: siswa.value.nis,
        nama: siswa.value.nama,
        jnskel: siswa.value.jnskel,
        tingkat: siswa.value.tingkat,
        jurusan: siswa.value.jurusan,
        kelas: siswa.value.kelas
      })
      .eq('id', id)
      .select();

    if (error) {
      console.error('Error updating data:', error.message);
      alert('Gagal memperbarui data siswa.');
      return;
    }

    alert('Data siswa berhasil diperbarui!');
    router.push('/admin/DataSiswa'); // Redirect ke halaman DataSiswa setelah update
  } catch (error) {
    console.error('Error updating siswa:', error.message);
    alert('Gagal memperbarui data siswa.');
  }
}


</script>

<style scoped>
.row {
  width: auto;
  height: 100vh;
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
