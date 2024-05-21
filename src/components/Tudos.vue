<template>
    <div>
      <div class="mb-3 d-flex justify-content-center">
        <input v-model="newActivity" type="text" class="form-control w-50" placeholder="Tambah kegiatan">
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
  </template>
  
  <script setup>
  import { ref, computed, watch } from 'vue';
  import Swal from 'sweetalert2';
  
  const props = defineProps(['activities']);
  const emit = defineEmits(['update-activities']);
  
  const newActivity = ref('');
  const filterActive = ref(false);
  
  const filteredActivities = computed(() => {
    if (filterActive.value) {
      return props.activities.filter(activity => !activity.completed);
    } else {
      return props.activities;
    }
  });
  
  const addActivity = () => {
    if (newActivity.value.trim() === '') return;
    const updatedActivities = [...props.activities, { name: newActivity.value, completed: false }];
    emit('update-activities', updatedActivities);
    newActivity.value = '';
    Swal.fire({
      icon: 'success',
      title: 'Kegiatan Ditambahkan!',
      showConfirmButton: false,
      timer: 1500
    });
  };
  
  const toggleStatus = (activity) => {
    activity.completed = !activity.completed;
    emit('update-activities', [...props.activities]);
    Swal.fire({
      icon: activity.completed ? 'success' : 'info',
      title: activity.completed ? 'Kegiatan Selesai!' : 'Kegiatan Belum Selesai!',
      showConfirmButton: false,
      timer: 1500
    });
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
        emit('update-activities', [...props.activities]);
        Swal.fire({
          icon: 'success',
          title: 'Kegiatan Diperbarui!',
          showConfirmButton: false,
          timer: 1500
        });
      }
    });
  };
  </script>
  
  <style scoped>
  .text-decoration-line-through {
    text-decoration: line-through;
  }
  .mb-3 {
    display: flex;
  }
  .good {
    margin-left: -20%;
  }
  table {
    border: 1px solid black;
    width: 150%;
    margin-left: 10%;
  }
  thead th {
    background-color: gray;
  }
  tbody td {
    background-color: white;
  }
  .text-center{
    margin-left: 25%;
  }
  </style>
  