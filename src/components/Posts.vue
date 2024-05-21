<template>
    <div>
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
  
  const props = defineProps(['initialPosts']);
  const emit = defineEmits(['update-posts']);
  
  const newUsername = ref('');
  const newComment = ref('');
  const selectedUsername = ref('');
  const selectedComment = ref('');
  
  const comments = ref(props.initialPosts);
  
  const usernames = computed(() => comments.value.map(comment => comment.username));
  
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
      emit('update-posts', comments.value);
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
        selectedComment.value = selectedUserComment.comment;
        Swal.fire({
          title: 'Data yang Dipilih',
          html: `<p>Username: ${selectedUserComment.username}<br>Komentar: ${selectedUserComment.comment}</p>`,
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
    }
  };
  </script>
  
  <style scoped>
  .postkan {
    position: relative;
    left: 36%;
    top: 10%;
    margin-top: -100px;
  }
  .list {
    position: fixed;
    left: 30%;
    margin-top: 1px;
  }
  .mb {
    margin-bottom: 10px;
  }
  .text-center {
    text-align: center;
  }
  </style>
  