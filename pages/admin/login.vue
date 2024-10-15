<template>
  <div class="container-fluid psh">
    <div class="row text-center pp">
      <div class="col">
        <h1 class="text-white">Halaman Admin</h1>
      </div>
    </div>
    <div class="row justify-content-center ps">
      <div class="col-lg-6 d-flex justify-content-center">
        <div class="card rounded-5 text-center pt-4">
          <h3 class="text-white">Login</h3>
          <div class="card-body d-flex justify-content-center pt-3">
            <form @submit.prevent="log">
              <label for="exampleInputEmail"></label>
              <input v-model="email" type="email" class="form-control form-control-lg rounded-3 akn text-center"
                id="exampleInputEmail" placeholder="Email">
              <label for="exampleInputPassword"></label>
              <input v-model="password" type="password" class="form-control form-control-lg rounded-3 akn text-center"
                id="exampleInputPassword" placeholder="Password">
              <button class="btn btn-success" type="submit">Login</button>
            </form>
          </div>
        </div>
      </div>
    </div>
    <div class="row">
      <div class="col text-white text-center pb-5">
        <p>&copy; CopyRightBy@Di-Tech</p>
      </div>
    </div>
    <div class="row kbl">
      <div class="col-lg-3">
        <nuxt-link to="/halamanUtama/">
          <button class="btn btn-light">Kembali</button>
        </nuxt-link>
      </div>
    </div>
  </div>
</template>

<script setup>
definePageMeta({
  layout: 'login'
})

const supa = useSupabaseClient()
const email = ref('')
const password = ref('')

// Objek berisi kombinasi email dan password yang diizinkan
const allowedCredentials = {
  'admin1@smkn4-tsm.sch.id': 'admin1smkn4', // Ganti dengan email dan password yang valid
  'admin2@smkn4-tsm.sch.id': 'admin2smkn4', // Ganti dengan email dan password yang valid
}

async function log() {
  console.log("Email: ", email.value);  // Cek email
  console.log("Password: ", password.value);  // Cek password

  // Cek apakah email dan password cocok
  if (!allowedCredentials[email.value] || allowedCredentials[email.value] !== password.value) {
    alert('Email atau Password tidak valid');
    return;
  }

  // Jika validasi berhasil, lanjutkan dengan login menggunakan Supabase
  const { data, error } = await supa.auth.signInWithPassword({
    email: email.value,
    password: password.value,
  });

  const loginStatus = error ? 'failure' : 'success';

  const { data: insertData, error: insertError } = await supa.from('log_activity').insert([
    {
      email: email.value,
      login_time: new Date().toISOString(),
      status: loginStatus
    }
  ]);

  if (insertError) {
    console.error("Error inserting log:", insertError);
  } else {
    console.log("Log successfully inserted:", insertData);
  }

  // Jika login berhasil, redirect ke halaman admin
  if (!error) {
    navigateTo('/admin/');
  } else {
    alert('Email atau Password Invalid');
  }
}

</script>

<style scoped>
.psh {
  min-height: 100vh;
  background: rgb(26, 26, 26) !important;
}

.kbl {
  padding-top: 20px;
  padding-left: 40px;
  padding-bottom: 70px;
}

.btn {
  width: 150px;
  height: 40px;
}

.akn {
  width: 250px;
  margin-bottom: 30px;
}

* {
  margin: 0px;
  padding: 0px;
}

.pp {
  padding-top: 70px;
  padding-bottom: 50px;
}

.card {
  width: 350px;
  height: 350px;
  box-shadow: 15px 15px 0px 5px gray;
  border: 2px solid gray;
  background: rgb(26, 26, 26);
}

.ps {
  padding-bottom: 50px;
}
</style>
