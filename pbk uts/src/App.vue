<script setup>
import { ref, computed, onMounted } from 'vue';

const activities = ref([]);
const newActivity = ref('');
const showUnfinishedOnly = ref(false);

function addActivity() {
  const name = newActivity.value.trim();
  if (name) {
    activities.value.push({ name, done: false });
    newActivity.value = '';
  }
}

function removeActivity(index) {
  activities.value.splice(index, 1);
}

function markAllAsDone() {
  activities.value.forEach(activity => {
    if (!activity.done) {
      activity.done = true;
    }
  });
}

const filteredActivities = computed(() => {
  if (showUnfinishedOnly.value) {
    return activities.value.filter(activity => !activity.done);
  }
  return activities.value;
});

// Animasi bintang jatuh
onMounted(() => {
  const canvas = document.getElementById('star-canvas');
  const ctx = canvas.getContext('2d');
  let width = (canvas.width = window.innerWidth);
  let height = (canvas.height = window.innerHeight);

  const stars = Array.from({ length: 100 }, () => ({
    x: Math.random() * width,
    y: Math.random() * height,
    size: Math.random() * 2,
    speed: Math.random() * 2 + 0.5
  }));

  function animate() {
    ctx.clearRect(0, 0, width, height);
    ctx.fillStyle = 'white';
    stars.forEach(star => {
      ctx.beginPath();
      ctx.arc(star.x, star.y, star.size, 0, Math.PI * 2);
      ctx.fill();
      star.y += star.speed;
      if (star.y > height) {
        star.y = 0;
        star.x = Math.random() * width;
      }
    });
    requestAnimationFrame(animate);
  }

  animate();

  window.addEventListener('resize', () => {
    width = canvas.width = window.innerWidth;
    height = canvas.height = window.innerHeight;
  });
});
</script>

<template>
  <div class="app-wrapper">
    <canvas id="star-canvas"></canvas>

    <div class="app-container">
      <h1 class="title">Daftar Kegiatan</h1>

      <div class="input-section">
        <input v-model="newActivity" @keyup.enter="addActivity" placeholder="Tambah kegiatan baru..." />
        <button @click="addActivity">Tambah</button>
      </div>

      <div class="filter-section">
        <label>
          <input type="checkbox" v-model="showUnfinishedOnly" />
          Kegiatan yang belum selesai
        </label>
      </div>

      <button @click="markAllAsDone" class="mark-all-done-btn">Tandai Semua Selesai</button>

      <ul class="activity-list">
        <li v-for="(activity, index) in filteredActivities" :key="index" class="activity-item">
          <label>
            <input type="checkbox" v-model="activity.done" />
            <span :class="{ done: activity.done }">{{ activity.name }}</span>
          </label>
          <button class="delete-btn" @click="removeActivity(index)">Hapus</button>
        </li>
      </ul>
      
      <div class="footer">
        <p>Made by RDN</p>
      </div>
    </div>
  </div>
</template>

<style scoped>
#star-canvas {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  z-index: 0;
  background: linear-gradient(to bottom, #001d3d, #003566);
}

.app-wrapper {
  position: relative;
  z-index: 1;
}

.app-container {
  max-width: 600px;
  margin: 80px auto;
  padding: 30px;
  background: linear-gradient(145deg, #e0f0ff, #ffffff);
  box-shadow: 10px 10px 30px #b0c4de, -10px -10px 30px #ffffff;
  border-radius: 20px;
  font-family: 'Poppins', sans-serif;
  transform: perspective(1000px) rotateX(2deg);
}

.title {
  text-align: center;
  color: #0056b3;
  font-size: 2rem;
  margin-bottom: 25px;
  font-weight: bold;
  text-shadow: 1px 1px 2px #87cefa;
}

.input-section {
  display: flex;
  gap: 10px;
  margin-bottom: 20px;
}

.input-section input {
  flex: 1;
  padding: 12px;
  border: 1px solid #90caf9;
  border-radius: 12px;
  background: #e3f2fd;
  box-shadow: inset 3px 3px 6px #b0c4de, inset -3px -3px 6px #ffffff;
  font-size: 1rem;
  color: #0d47a1;
}

.input-section button {
  padding: 12px 24px;
  background: linear-gradient(135deg, #42a5f5, #1e88e5);
  color: white;
  border: none;
  border-radius: 12px;
  cursor: pointer;
  font-weight: bold;
  transition: all 0.3s ease;
  box-shadow: 0 4px 10px rgba(30, 136, 229, 0.5);
}

.input-section button:hover {
  background: linear-gradient(135deg, #1e88e5, #1565c0);
  transform: translateY(-2px);
}

.filter-section {
  margin-bottom: 20px;
  text-align: center;
  color: #1565c0;
  font-weight: 500;
}

.activity-list {
  list-style: none;
  padding: 0;
}

.activity-item {
  display: flex;
  align-items: center;
  justify-content: space-between;
  padding: 12px;
  background: linear-gradient(145deg, #ffffff, #e3f2fd);
  border: 1px solid #90caf9;
  border-radius: 12px;
  margin-bottom: 12px;
  box-shadow: 4px 4px 10px #b0c4de, -4px -4px 10px #ffffff;
  transition: transform 0.2s;
}

.activity-item:hover {
  transform: scale(1.02);
}

.activity-item label {
  display: flex;
  align-items: center;
  gap: 10px;
  color: #000000;
}

.activity-item .done {
  text-decoration: line-through;
  color: #90a4ae;
}

.delete-btn {
  background: linear-gradient(135deg, #ef5350, #e53935);
  color: white;
  border: none;
  padding: 8px 16px;
  border-radius: 8px;
  cursor: pointer;
  font-weight: bold;
  box-shadow: 0 4px 10px rgba(229, 57, 53, 0.5);
  transition: all 0.3s ease;
}

.delete-btn:hover {
  background: linear-gradient(135deg, #e53935, #c62828);
  transform: translateY(-2px);
}

.mark-all-done-btn {
  display: block;
  width: 100%;
  padding: 12px;
  background: linear-gradient(135deg, #66bb6a, #388e3c);
  color: white;
  border: none;
  border-radius: 12px;
  font-weight: bold;
  cursor: pointer;
  box-shadow: 0 4px 10px rgba(56, 142, 108, 0.5);
  transition: all 0.3s ease;
  margin-bottom: 20px;
}

.mark-all-done-btn:hover {
  background: linear-gradient(135deg, #388e3c, #2c6f2e);
  transform: translateY(-2px);
}

.footer {
  color:#1565c0;
}
</style>
