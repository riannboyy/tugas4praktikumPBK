<template>
  <div class="min-h-screen bg-gradient-to-b from-gray-900 to-gray-800 text-white flex flex-col items-center px-4 py-10">
    <h1 class="text-4xl font-bold mb-8 text-blue-400">📝 Vue Note App</h1>

    <div class="bg-gray-900 p-6 rounded-2xl shadow-2xl w-full max-w-xl mb-6 border border-gray-700">
      <div class="mb-4">
        <label class="block mb-1 text-sm text-gray-300">Judul</label>
        <input
          v-model="judul"
          placeholder="Masukkan judul"
          class="w-full p-3 rounded-lg bg-gray-700 text-white placeholder-gray-400 focus:outline-none focus:ring-2 focus:ring-blue-500"
        />
      </div>
      <div class="mb-4">
        <label class="block mb-1 text-sm text-gray-300">Konten</label>
        <textarea
          v-model="konten"
          placeholder="Tulis isi konten"
          class="w-full p-3 rounded-lg bg-gray-700 text-white placeholder-gray-400 resize-none focus:outline-none focus:ring-2 focus:ring-blue-500"
        ></textarea>
      </div>

      <div class="flex items-center justify-between">
        <button
          @click="simpanData"
          class="bg-blue-600 hover:bg-blue-700 transition-all text-white py-2 px-5 rounded-lg font-semibold"
        >
          {{ isEdit ? '✏️ Update' : '💾 Save' }}
        </button>
        <span v-if="isEdit" class="text-sm text-yellow-400">🔧 Sedang mengedit...</span>
      </div>
    </div>

    <transition-group name="fade" tag="div" class="w-full max-w-xl space-y-4">
      <div
        v-for="item in data"
        :key="item.id"
        class="bg-gray-800 rounded-xl p-5 shadow-lg border border-gray-700 hover:scale-[1.01] transition-transform"
      >
        <div class="flex justify-between items-center mb-2">
          <h2 class="text-xl font-semibold text-white">{{ item.judul }}</h2>
          <div class="flex gap-2">
            <button
              @click="editData(item)"
              class="bg-yellow-500 hover:bg-yellow-600 text-black px-3 py-1 rounded-md text-sm"
            >
              ✏️ Edit
            </button>
            <button
              @click="hapusData(item.id)"
              class="bg-red-600 hover:bg-red-700 text-white px-3 py-1 rounded-md text-sm"
            >
              🗑️ Delete
            </button>
          </div>
        </div>
        <p class="text-gray-300">{{ item.konten }}</p>
      </div>
    </transition-group>

    <button
      @click="loadData"
      class="mt-8 bg-green-600 hover:bg-green-700 px-6 py-2 rounded-lg text-white font-semibold"
    >
      🔄 Reload Data
    </button>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue'
import axios from 'axios'

const baseURL = 'http://localhost:3000/notes'

const judul = ref('')
const konten = ref('')
const data = ref([])
const isEdit = ref(false)
const editId = ref(null)

const loadData = async () => {
  const res = await axios.get(baseURL)
  data.value = res.data.reverse() // biar terbaru di atas
}

const simpanData = async () => {
  if (!judul.value || !konten.value) return

  if (isEdit.value) {
    await axios.put(`${baseURL}/${editId.value}`, {
      judul: judul.value,
      konten: konten.value,
    })
    isEdit.value = false
    editId.value = null
  } else {
    await axios.post(baseURL, {
      judul: judul.value,
      konten: konten.value,
    })
  }

  judul.value = ''
  konten.value = ''
  loadData()
}

const editData = (item) => {
  judul.value = item.judul
  konten.value = item.konten
  isEdit.value = true
  editId.value = item.id
}

const hapusData = async (id) => {
  await axios.delete(`${baseURL}/${id}`)
  loadData()
}

onMounted(() => {
  loadData()
})
</script>

<style scoped>
.fade-enter-active,
.fade-leave-active {
  transition: all 0.3s ease;
}
.fade-enter-from,
.fade-leave-to {
  opacity: 0;
  transform: translateY(10px);
}
</style>
