<template>
    <div class="container-fluid">
        <div class="row">
            <div class="col-3 bg-primary">
                <div class="d-flex justify-content-center p-4">
                    <nuxt-link to="/admin/" class="text-decoration-none text-light nav-link">Home</nuxt-link>
                </div>
                <div class="d-flex justify-content-center p-4">
                    <nuxt-link to="/admin/DataSiswa/" class="text-decoration-none text-light nav-link">Data
                        Siswa</nuxt-link>
                </div>
                <div class="d-flex justify-content-center p-4">
                    <nuxt-link to="/admin/dataPresensi" class="text-decoration-none text-light nav-link">Data
                        Presensi</nuxt-link>
                </div>
                <div class="d-flex justify-content-center p-4">
                    <nuxt-link to="/admin/dataUsers/" class="text-decoration-none text-light nav-link">Data
                        Users</nuxt-link>
                </div>
            </div>
            <div class="col-9 bg-danger-subtle">
                <div class="d-flex justify-content-center pt-5 pb-5">
                    <h1>Data Presensi</h1>
                </div>
                <div>
                    <table border="2">
                        <thead>
                            <tr>
                                <th>No</th>
                                <th>Tanggal</th>
                                <th>Nama</th>
                                <th>Kelas</th>
                            </tr>
                        </thead>
                        <tbody>
                            <tr v-for="(visitor, i) in paginatedVisitors" :key="i">
                                <td>{{ i + 1 + (currentPage - 1) * perPage }}</td>
                                <td>{{ visitor.tanggal }}</td>
                                <td>{{ visitor.siswa.nama }}</td>
                                <td>{{ visitor.siswa?.tingkat }} {{ visitor.jurusan?.nama }} {{ visitor.siswa?.kelas }}
                                </td>
                            </tr>
                        </tbody>
                    </table>

                    <!-- Pagination Controls -->
                    <div class="d-flex justify-content-center mt-5 pb-5">
                        <button @click="prevPage" :disabled="currentPage === 1"
                            class="btn btn-primary me-2">Previous</button>
                        <span>Page {{ currentPage }} of {{ totalPages }}</span>
                        <button @click="nextPage" :disabled="currentPage === totalPages"
                            class="btn btn-primary ms-2">Next</button>
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
const visitors = ref([])
const currentPage = ref(1)
const perPage = 35

// Fungsi untuk mendapatkan data log aktivitas
const getlogActivity = async () => {
    try {
        console.log("Fetching log activity data...")

        const { data, error } = await supabase
            .from('presensi')
            .select('*, tanggal,siswa(id, nama, tingkat, kelas), keterangan(nama), jurusan(nama)')

        if (error) {
            console.error("Error fetching log activity:", error)
        }

        if (data) {
            console.log("Log activity data received:", data)
            // Urutkan data berdasarkan tanggal terbaru
            visitors.value = data.sort((a, b) => new Date(b.tanggal) - new Date(a.tanggal))
        }

    } catch (e) {
        console.error("Unexpected error:", e)
    }
}

// Fungsi untuk memformat waktu login
const formatDate = (dateString) => {
    const options = {
        year: 'numeric',
        month: 'long',
        day: 'numeric',
        hour: '2-digit',
        minute: '2-digit',
        timeZone: 'Asia/Jakarta'
    }
    return new Date(dateString).toLocaleString('id-ID', options)
}

const paginatedVisitors = computed(() => {
    const start = (currentPage.value - 1) * perPage;
    const end = start + perPage;
    return visitors.value.slice(start, end);
});

const totalPages = computed(() => {
    return Math.ceil(visitors.value.length / perPage);
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

onMounted(() => {
    getlogActivity()
})
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

/* CSS untuk efek hover */
.nav-link {
    width: 100%;
    padding: 10px;
    display: block;
    transition: background-color 0.3s ease, color 0.3s ease;
    border-radius: 5px;
}

/* Efek ketika kursor hover di atas link navigasi */
.nav-link:hover {
    background-color: #ff0000;
    /* Warna background berubah menjadi abu-abu terang */
    color: #007bff;
    /* Warna teks berubah menjadi biru */
    font-weight: bold;
    /* Membuat teks lebih tebal saat hover */
    cursor: pointer;
    /* Menampilkan pointer ketika hover */
}
</style>
