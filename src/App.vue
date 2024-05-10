<template>
  <div class="background">
    <img src="https://img.freepik.com/free-photo/pink-sky-background-with-crescent-moon_53876-129048.jpg?w=996&t=st=1715252408~exp=1715253008~hmac=b1fff2fb0b7c0d3f22ce41a6792152f37483f5da2fd82e4e16511ad0c694c915"/>
  </div>
  <div class="container mt-4 ">
    <nav class="navbar navbar-expand-lg navbar-dark ">
      <div class="container-fluid justify-content-center ">
        <ul class="navbar-nav">
          <li class="nav-item">
            <button class="nav-link" href="#" @click.prevent="activeTab = 'todos'" style="color: gray;">Todos</button>
          </li>
          <li class="nav-item">
            <button class="nav-link" href="#" @click.prevent="activeTab = 'post'" style="color: gray;">Post</button>
          </li>
        </ul>
      </div>
    </nav>
  </div>

  <div v-if="activeTab === 'todos'">
    <div class="mb-3 d-flex justify-content-center">
      <input v-model="newActivity" type="text" class="form-control w-50" placeholder="Tambah kegiatan" >
      <button @click="addActivity" class="btn btn-primary ms-2">Tambah</button>
    </div>
    <div class="text-center">
      <button @click="toggleFilter" :class="{'btn-dark': filterActive, 'btn-secondary': !filterActive}" class="btn mb-3 good">
        {{ filterActive ? 'Tampilkan Semua' : 'Tampilkan Belum Selesai' }}
      </button>
    </div>
    <table>
      <thead class="thead-dark">
        <tr>
          <th>Kegiatan</th>
          <th>Status</th>
          <th>Edit</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="(activity, index) in filteredActivities" :key="index">
          <td :class="{'text-decoration-line-through': activity.completed}">
            {{ activity.name }}
          </td>
          <td>
            <input type="checkbox" v-model="activity.completed" @change="toggleStatus(activity)">
          </td>
          <td>
            <button @click="editActivity(activity)" class="btn btn-info">Edit</button>
          </td>
        </tr>
      </tbody>
    </table>
  </div>
  <div v-if="activeTab === 'post'">
    <div class="postkan">
      <div class="mb text-center">
        <label for="username" class="username">Username:</label>
        <input v-model="newUsername" type="text" class="form-control username" placeholder="Masukkan username">
      </div>
      <div class="mb text-center">
        <label for="comment" class="komen">Komentar:</label>
        <textarea id="comment" v-model="newComment" class="form-control komen" placeholder="Masukkan komentar"></textarea>
      </div>
      <button @click="submitPost" class="btn btn-primary">Post</button>
    </div>

    <div class="list">
    <div class="dropdown mt-3 text-center">
      <select v-model="selectedUsername" @change="selectUsername(selectedUsername)" class="btn btn-secondary">
  <option value="" selected>Pilih Pengguna</option>
  <option v-for="username in usernames" :key="username">{{ username }}</option>
</select>

    </div>
    

    <!-- Menampilkan Data Pengguna yang Dipilih -->
    <div v-if="selectedUsername" class="mt-3">
      <h3 class="text-center">Data yang Dipilih:</h3>
      <p class="text-center">Username: <strong>{{ selectedUsername }}</strong></p>
      <p class="text-center">Komentar: <strong>{{ selectedComment }}</strong></p>
    </div>
  </div>
  </div>
</template>

<script setup>
import { ref, computed } from 'vue';
import Swal from 'sweetalert2';
import axios from 'axios';

const activeTab = ref('todos');
const newActivity = ref('');
const filterActive = ref(false);
const activities = ref([
  { name: 'Membaca buku', completed: false },
  { name: 'Menulis laporan', completed: true },
  { name: 'Bersantai di taman', completed: true },
]);

const newUsername = ref('');
const newComment = ref('');
const selectedUsername = ref('');
const selectedComment = ref('');
const alertMessage = ref('');
const comments = ref([
  { id: 1, username: 'Raka Gembung', comment: 'Sangat membantu' },
  { id: 2, username: 'Dara', comment: 'Saya bisa menambah list' },
  { id: 3, username: 'Gendrung', comment: 'Free ide' },
]);

const usernames = computed(() => comments.value.map(comment => comment.username));

const filteredActivities = computed(() => {
  if (filterActive.value) {
    return activities.value.filter(activity => !activity.completed);
  } else {
    return activities.value;
  }
});

const addActivity = () => {
  if (newActivity.value.trim() === '') return;
  activities.value.push({ name: newActivity.value, completed: false });
  newActivity.value = '';
  Swal.fire({
    icon: 'success',
    title: 'Kegiatan Ditambahkan!',
    showConfirmButton: false,
    timer: 1500
  });
};

const toggleStatus = (activity) => {
  if (activity.completed) {
    Swal.fire({
      icon: 'success',
      title: 'Kegiatan Selesai!',
      showConfirmButton: false,
      timer: 1500
    });
  } else {
    Swal.fire({
      icon: 'info',
      title: 'Kegiatan Belum Selesai!',
      showConfirmButton: false,
      timer: 1500
    });
  }
};

const toggleFilter = () => {
  filterActive.value = !filterActive.value;
};

const editActivity = (activity) => {
  Swal.fire({
    title: 'Edit Kegiatan',
    input: 'text',
    inputValue: activity.name,
    showCancelButton: true,
    confirmButtonText: 'Simpan',
    cancelButtonText: 'Batal',
    inputValidator: (value) => {
      if (!value) {
        return 'Kolom tidak boleh kosong!';
      }
    }
  }).then((result) => {
    if (result.isConfirmed) {
      activity.name = result.value;
      Swal.fire({
        icon: 'success',
        title: 'Kegiatan Diperbarui!',
        showConfirmButton: false,
        timer: 1500
      });
    }
  });
};

const submitPost = async () => {
  try {
    if (!newUsername.value || !newComment.value) {
      Swal.fire({
        icon: 'error',
        title: 'Data Belum Lengkap',
        text: 'Harap masukkan username dan komentar.',
        confirmButtonText: 'OK'
      });
      return;
    }
    const response = await axios.post('https://jsonplaceholder.typicode.com/comments', {
      username: newUsername.value,
      comment: newComment.value,
    });
    comments.value.push(response.data);
    newComment.value = '';
    Swal.fire({
      icon: 'success',
      title: 'Komentar Berhasil Diposting!',
      showConfirmButton: false,
      timer: 1500
    });
  } catch (error) {
    console.error(error);
  }
};

const selectUsername = (username) => {
  if (username === '') {
    Swal.fire({
      title: 'Data yang Dipilih',
      text: 'Silakan pilih pengguna terlebih dahulu.',
      icon: 'info',
      confirmButtonText: 'OK'
    });
  } else {
    const selectedUserComment = comments.value.find(comment => comment.username === username);
    if (selectedUserComment) {
      const alertMessage = `Username: ${selectedUserComment.username}`;
      const alertMessage2 =`Komentar: ${selectedUserComment.comment}`;
      Swal.fire({
        title: 'Data yang Dipilih',
        html: `<p>${alertMessage}<br>${alertMessage2}</p>`,
        icon: 'info',
        confirmButtonText: 'OK'
      });
    } else {
      Swal.fire({
        title: 'Data yang Dipilih',
        text: 'Tidak ada komentar yang terkait dengan pengguna ini.',
        icon: 'info',
        confirmButtonText: 'OK'
      });
    }
    selectedUsername.value = '';
  }
};

</script>

<style>
.postkan{
  position: relative;
  left: 36%;
  top: 10%;
  margin-top: -100px;
}
.list{
  position: fixed;
  left: 30%;
  margin-top: 1px;
}


.mb {
  margin-bottom: 10px;
}


.container input{
  margin-left: 100%;
  z-index: 1;
}

.text-decoration-line-through {
  text-decoration: line-through;
}
.mb-3{
  display: flex;
  
}

.btn-secondary{
  margin-left: -50%;
}

.heder ul, li{
  position: relative;
  text-decoration: none;

}
.heder li{
    margin-right: 150px;
    border: 1px solid white; 
}
.navbar{
  position: absolute;
  margin-top: -200px;
  top: -300%;
  left: -50%;

}

.nav-item{
  margin-right: 100px;

}
.navbar button{
  border: 1px solid white;
  width: 100px;
}
.navbar button:hover{
  font-size: 14px;
  background-color: white;
}



.background img{
  position: fixed;
  top: 0;
  left: 0;
  min-height: 90%;
  min-width: 1500px;
  width: 100%;
  height: 100%;
  overflow: hidden;
  z-index: -2;
  object-fit: cover;
  -webkit-background-size:cover; -moz-background-size:cover; -o-background-size:cover; background-size: cover;
}
.good{
  margin-left: -20%;
}
table{
  border: 1px solid black;
  width: 150%;
  margin-left: 10%;
}

tr th {
  background-color: gray;
}

tr td{
  background-color: white;
}

</style>
