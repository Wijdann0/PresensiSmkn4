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
                <div class="d-flex justify-content-center pt-3 pb-3">
                    <h1>Log Activity</h1>
                </div>
                <div>
                    <table border="2">``
                        <thead>
                            <tr>
                                <th>No</th>
                                <th>Email</th>
                                <th>Waktu Login</th>
                                <th>Status</th>
                            </tr>
                        </thead>
                        <tbody>
                            <tr v-for="(visitor, i) in visitors" :key="i">
                                <td>{{ i + 1 }}</td>
                                <td>{{ visitor.email }}</td>
                                <td>{{ formatDate(visitor.login_time) }}</td>
                                <td>{{ visitor.status }}</td>
                            </tr>
                        </tbody>
                    </table>
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

// Fungsi untuk mendapatkan data log aktivitas
const getlogActivity = async () => {
    try {
        console.log("Fetching log activity data...")

        const { data, error } = await supabase
            .from('log_activity')
            .select('*')

        if (error) {
            console.error("Error fetching log activity:", error)
        }

        if (data) {
            console.log("Log activity data received:", data)
            visitors.value = data
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

th, td {
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
    background-color: #ff0000; /* Warna background berubah menjadi abu-abu terang */
    color: #007bff;           /* Warna teks berubah menjadi biru */
    font-weight: bold;         /* Membuat teks lebih tebal saat hover */
    cursor: pointer;           /* Menampilkan pointer ketika hover */
}
</style>
