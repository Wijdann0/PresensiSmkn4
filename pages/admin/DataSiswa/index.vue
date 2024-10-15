<template>
    <div class="container-fluid">
        <div class="row">
            <div class="col-3 bg-primary">
                <div class="d-flex justify-content-center p-4">
                    <nuxt-link to="/admin/" class="text-decoration-none text-light nav-link">Home</nuxt-link>
                </div>
                <div class="d-flex justify-content-center p-4">
                    <nuxt-link to="/admin/DataSiswa/" class="text-decoration-none text-light nav-link">Data Siswa</nuxt-link>
                </div>
                <div class="d-flex justify-content-center p-4">
                    <nuxt-link to="/admin/dataPresensi" class="text-decoration-none text-light nav-link">Data Presensi</nuxt-link>
                </div>
                <div class="d-flex justify-content-center p-4">
                    <nuxt-link to="#" class="text-decoration-none text-light nav-link">Tambah Admin</nuxt-link>
                </div>
                <div class="d-flex justify-content-center p-4">
                    <nuxt-link to="#" class="text-decoration-none text-light nav-link">Data Admin</nuxt-link>
                </div>
            </div>
            <div class="col-9 bg-danger-subtle">
                <div class="d-flex justify-content-center pt-5 pb-3">
                    <h1>Data Siswa</h1>
                </div>

                <!-- Filter Section -->
                <div class="d-flex justify-content-around p-3">
                    <div>
                        <input v-model="filterTingkat" id="filterTingkat" placeholder="Pilih Kelas" class="form form-control">
                    </div>
                    <div>
                        <select v-model="filterJurusan" id="filterJurusan" class="form form-control">
                            <option value="" disabled selected>Pilih Jurusan</option>
                            <option v-for="jur in jurusan" :key="jur.id" :value="jur.nama">{{ jur.nama }}</option>
                        </select>
                    </div>
                    <div>
                        <input v-model="filterKelas" id="filterKelas" placeholder="Pilih No Kelas" class="form form-control">
                    </div>
                    <div>
                        <nuxt-link to="/admin/DataSiswa/tambah">
                            <button class="btn btn-primary">Tambah Siswa</button>
                        </nuxt-link>
                    </div>
                </div>

                <div>
                    <table border="2">
                        <thead>
                            <tr>
                                <th>No</th>
                                <th>NISN</th>
                                <th>NAMA</th>
                                <th>JENIS KELAMIN</th>
                                <th>KELAS</th>
                                <th>JURUSAN</th>
                                <th>NO KELAS</th>
                                <th colspan="2">Custom</th>
                            </tr>
                        </thead>
                        <tbody>
                            <tr v-for="(siwa, i) in paginatedSiswa" :key="siwa.id">
                                <td>{{ i + 1 + (currentPage - 1) * perPage }}</td>
                                <td>{{ siwa.nis }}</td>
                                <td>{{ siwa.nama }}</td>
                                <td>{{ siwa.jnskel }}</td>
                                <td>{{ siwa.tingkat }}</td>
                                <td>{{ siwa.jurusan.nama }}</td>
                                <td>{{ siwa.kelas }}</td>
                                <td>
                                    <button @click="editSiswa(siwa)" class="btn btn-warning">Edit</button>
                                </td>
                                <td>
                                    <button @click="deleteSiswa(siwa.id)" class="btn btn-danger">Delete</button>
                                </td>
                            </tr>
                        </tbody>
                    </table>

                    <!-- Pagination Controls -->
                    <div class="d-flex justify-content-center mt-5 pb-5">
                        <button @click="prevPage" :disabled="currentPage === 1" class="btn btn-primary me-2">Previous</button>
                        <span>Page {{ currentPage }} of {{ totalPages }}</span>
                        <button @click="nextPage" :disabled="currentPage === totalPages" class="btn btn-primary ms-2">Next</button>
                    </div>
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
const siswa = ref([])
const jurusan = ref([])
const currentPage = ref(1)
const perPage = 50 // Jumlah item per halaman

// Filter states
const filterTingkat = ref('')
const filterJurusan = ref('')
const filterKelas = ref('')

// Computed property untuk memfilter data
const filteredSiswa = computed(() => {
    return siswa.value.filter(siwa => {
        const matchTingkat = filterTingkat.value
            ? siwa.tingkat && siwa.tingkat.toString().toLowerCase().includes(filterTingkat.value.toLowerCase())
            : true;
        const matchJurusan = filterJurusan.value
            ? siwa.jurusan && siwa.jurusan.nama.toLowerCase() === filterJurusan.value.toLowerCase()
            : true;
        const matchKelas = filterKelas.value
            ? siwa.kelas && siwa.kelas.toString().toLowerCase().includes(filterKelas.value.toLowerCase())
            : true;

        return matchTingkat && matchJurusan && matchKelas;
    });
});

// Pagination logic after filtering
const paginatedSiswa = computed(() => {
    const start = (currentPage.value - 1) * perPage;
    const end = start + perPage;
    return filteredSiswa.value.slice(start, end);
});

const totalPages = computed(() => {
    return Math.ceil(filteredSiswa.value.length / perPage);
});

const nextPage = () => {
    if (currentPage.value < totalPages.value) {
        currentPage.value++;
    }
}

const prevPage = () => {
    if (currentPage.value > 1) {
        currentPage.value--;
    }
}

const getSiswa = async () => {
    currentPage.value = 1; // Reset halaman ke 1 saat data diambil
    siswa.value = []; // Reset data siswa sebelum pengambilan

    // Loop untuk mengambil semua data dari tabel siswa
    let offset = 0;
    const limit = 50; // Mengambil 50 data per panggilan
    let allDataFetched = false; // Flag untuk mengecek apakah semua data telah diambil

    while (!allDataFetched) {
        const { data, error } = await supabase
            .from('siswa')
            .select('*, jurusan(nama)')
            .range(offset, offset + limit - 1);

        if (error) {
            console.error(error);
            break;
        }

        if (data.length > 0) {
            siswa.value = [...siswa.value, ...data]; // Tambahkan data baru ke array siswa
            offset += limit; // Naikkan offset untuk panggilan berikutnya
        } else {
            allDataFetched = true; // Semua data sudah diambil
        }
    }

    // Setelah semua data didapatkan, urutkan berdasarkan kelas dan nama
    siswa.value.sort((a, b) => {
        const kelasComparison = a.kelas - b.kelas;
        if (kelasComparison !== 0) {
            return kelasComparison;
        } else {
            return a.nama.localeCompare(b.nama);
        }
    });
};

const getJurusan = async () => {
    const { data, error } = await supabase
        .from('jurusan')
        .select('*');

    if (data) {
        jurusan.value = data;
    }
};

const editSiswa = async (siwa) => {
    const { id } = siwa;
    window.location.href = `/admin/DataSiswa/edit/${id}`;
};

const deleteSiswa = async (id) => {
    try {
        console.log("Deleting siswa with ID:", id);

        const { data, error } = await supabase
            .from('siswa')
            .delete()
            .eq('id', id);

        if (error) {
            console.error("Error deleting siswa from database:", error.message);
            alert("Terjadi kesalahan saat menghapus data dari database!");
        } else {
            console.log("Siswa deleted successfully from database:", data);
            siswa.value = siswa.value.filter(siwa => siwa.id !== id);
            alert("Data siswa berhasil dihapus dari database!");
        }
    } catch (err) {
        console.error("Unexpected error:", err);
    }
};

onMounted(() => {
    getSiswa();
    getJurusan();
});
</script>

<style scoped>
table {
    width: 100%;
    text-align: center;
    border-collapse: collapse;
}

th,
td {
    padding: 10px;
}

th {
    background-color: #f2f2f2;
}

tr:nth-child(even) {
    background-color: #f9f9f9;
}

.row {
    min-height: 100vh;
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
</style>
