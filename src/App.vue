<template>
  <div class="background">
    <img src="https://img.freepik.com/free-photo/pink-sky-background-with-crescent-moon_53876-129048.jpg?w=996&t=st=1715252408~exp=1715253008~hmac=b1fff2fb0b7c0d3f22ce41a6792152f37483f5da2fd82e4e16511ad0c694c915"/>
  </div>
  <div class="container mt-4">
    <nav class="navbar navbar-expand-lg navbar-dark">
      <div class="container-fluid justify-content-center">
        <ul class="navbar-nav">
          <li class="nav-item">
            <button class="nav-link" @click.prevent="activeTab = 'todos'" :style="{ color: activeTab === 'todos' ? 'white' : 'gray' }">Todos</button>
          </li>
          <li class="nav-item">
            <button class="nav-link" @click.prevent="activeTab = 'posts'" :style="{ color: activeTab === 'posts' ? 'white' : 'gray' }">Posts</button>
          </li>
        </ul>
      </div>
    </nav>
  </div>

  <div v-if="activeTab === 'todos'">
    <Todos :activities="activities" @update-activities="updateActivities" />
  </div>
  <div v-if="activeTab === 'posts'">
    <Posts :initialPosts="comments" @update-posts="updateComments" />
  </div>
</template>

<script setup>
import { ref } from 'vue';
import Todos from './components/Tudos.vue';
import Posts from './components/Posts.vue';

const activeTab = ref('todos');
const activities = ref([
  { name: 'Membaca buku', completed: false },
  { name: 'Menulis laporan', completed: true },
  { name: 'Bersantai di taman', completed: true },
]);

const comments = ref([
  { id: 1, username: 'Raka Gembung', comment: 'Sangat membantu' },
  { id: 2, username: 'Dara', comment: 'Saya bisa menambah list' },
  { id: 3, username: 'Gendrung', comment: 'Free ide' },
]);

const updateActivities = (updatedActivities) => {
  activities.value = updatedActivities;
};

const updateComments = (updatedComments) => {
  comments.value = updatedComments;
};
</script>

<style>
.background img {
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
  background-size: cover;
}

.container input {
  margin-left: 100%;
  z-index: 1;
}

.navbar {
  position: absolute;
  margin-top: -200px;
  top: -300%;
  left: -50%;
}

.nav-item {
  margin-right: 100px;
}

.navbar button {
  border: 1px solid white;
  width: 100px;
}

.navbar button:hover {
  font-size: 14px;
  background-color: white;
}
</style>
