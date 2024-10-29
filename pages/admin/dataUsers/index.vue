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
        <div class="d-flex justify-content-center pt-5 pb-3">
          <div class="card text-center">
            <div class="card-header">
              Data Users
            </div>
            <div class="card-body">
              <h5 class="card-title">Soon</h5>
              <p class="card-text">Sedang dilakukan Pengembangan</p>
              <a href="/admin/" class="btn btn-primary">Go back</a>
            </div>
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
