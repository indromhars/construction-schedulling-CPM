<template>
  <div class="min-h-screen bg-gray-100 py-8 px-4">
    <div class="container mx-auto bg-white shadow-md rounded-lg p-6">
      <h1 class="text-2xl font-bold text-gray-800 mb-6">CPM Project Scheduler</h1>

      <!-- Description Section -->
      <div class="text-sm text-gray-700 mb-6">
        <p>
          <strong>CPM Project Scheduler</strong> adalah aplikasi berbasis web yang dirancang untuk membantu manajer proyek dan tim dalam merencanakan, mengelola, dan menganalisis jadwal proyek menggunakan metode <strong>Critical Path Method (CPM)</strong>. CPM adalah teknik manajemen proyek yang digunakan untuk mengidentifikasi jalur paling kritis dalam proyek, yaitu rangkaian aktivitas yang menentukan durasi minimum proyek tersebut.
        </p>
        <p>Dengan alat ini, pengguna dapat:</p>
        <ul class="list-disc pl-6 space-y-2">
          <li>
            <strong>Menambahkan Aktivitas Proyek:</strong> Memasukkan detail aktivitas seperti nama, durasi, unit waktu, dan ketergantungan antar aktivitas.
          </li>
          <li>
            <strong>Menghitung Jalur Kritis:</strong> Menentukan <strong>Earliest Start (ES)</strong>, <strong>Earliest Finish (EF)</strong>, <strong>Latest Start (LS)</strong>, <strong>Latest Finish (LF)</strong>, serta <strong>Float</strong> setiap aktivitas. Float digunakan untuk mengetahui fleksibilitas waktu dalam menyelesaikan aktivitas tanpa memengaruhi durasi total proyek.
          </li>
          <li>
            <strong>Mengidentifikasi Aktivitas Kritis:</strong> Aktivitas dengan Float = 0 berada pada jalur kritis, sehingga harus diselesaikan tepat waktu agar proyek tidak tertunda.
          </li>
        </ul>
        <p>
          Aplikasi ini cocok digunakan untuk berbagai jenis proyek, baik konstruksi, pengembangan perangkat lunak, maupun proyek lain yang melibatkan banyak aktivitas terhubung. Dengan <strong>CPM Project Scheduler</strong>, pengelolaan proyek menjadi lebih terstruktur, memungkinkan tim untuk bekerja lebih efektif dan efisien.
        </p>
      </div>

      <!-- Form for Adding Activities -->
      <form @submit.prevent="addActivity" class="mb-6 space-y-4">
        <h2 class="text-xl font-semibold text-gray-700">Add Project Activity</h2>

        <div>
          <label class="block text-sm font-medium text-gray-700">Activity Name</label>
          <input
            v-model="newActivity.name"
            type="text"
            class="mt-1 block w-full rounded-md border-gray-300 shadow-sm focus:border-indigo-500 focus:ring-indigo-500 sm:text-sm"
            required
          />
        </div>

        <div>
          <label class="block text-sm font-medium text-gray-700">Duration</label>
          <div class="flex space-x-2">
            <input
              v-model.number="newActivity.duration"
              type="number"
              class="mt-1 block w-2/3 rounded-md border-gray-300 shadow-sm focus:border-indigo-500 focus:ring-indigo-500 sm:text-sm"
              required
            />
            <select
              v-model="newActivity.unit"
              class="mt-1 block w-1/3 rounded-md border-gray-300 shadow-sm focus:border-indigo-500 focus:ring-indigo-500 sm:text-sm"
            >
              <option value="days">Days</option>
              <option value="weeks">Weeks</option>
              <option value="months">Months</option>
            </select>
          </div>
        </div>

        <div>
          <label class="block text-sm font-medium text-gray-700">
            Dependency (Activity must finish before this starts)
          </label>
          <input
            v-model="newActivity.dependency"
            type="text"
            class="mt-1 block w-full rounded-md border-gray-300 shadow-sm focus:border-indigo-500 focus:ring-indigo-500 sm:text-sm"
          />
          <p class="text-sm text-gray-500 mt-1">
            Separate multiple dependencies with a comma (e.g., "Activity A, Activity B").
          </p>
        </div>

        <button
          type="submit"
          class="bg-indigo-600 text-white px-4 py-2 rounded-md hover:bg-indigo-700 focus:ring-2 focus:ring-indigo-500"
        >
          Add Activity
        </button>
      </form>

      <!-- Activities Table -->
      <h2 class="text-xl font-semibold text-gray-700 mb-4">Project Activities</h2>

      <p class="text-sm text-gray-600 mb-4">
        <strong>Earliest Start (ES):</strong> Waktu paling awal sebuah aktivitas bisa dimulai berdasarkan aktivitas pendahulunya.<br>
        <strong>Earliest Finish (EF):</strong> Waktu paling awal sebuah aktivitas bisa selesai, dihitung dari ES + Durasi.<br>
        <strong>Latest Start (LS):</strong> Waktu paling lambat sebuah aktivitas bisa dimulai tanpa menyebabkan penundaan proyek.<br>
        <strong>Latest Finish (LF):</strong> Waktu paling lambat sebuah aktivitas bisa selesai tanpa menunda proyek.<br>
        <strong>Float:</strong> Waktu cadangan sebuah aktivitas yang dapat digunakan tanpa memengaruhi durasi total proyek. Jika Float = 0, aktivitas berada di jalur kritis.
      </p>

      <table class="min-w-full bg-white border border-gray-200 rounded-lg">
        <thead class="bg-gray-50">
          <tr>
            <th class="py-2 px-4 border-b text-left text-sm font-semibold text-gray-600">Activity</th>
            <th class="py-2 px-4 border-b text-left text-sm font-semibold text-gray-600">Duration</th>
            <th class="py-2 px-4 border-b text-left text-sm font-semibold text-gray-600">Unit</th>
            <th class="py-2 px-4 border-b text-left text-sm font-semibold text-gray-600">Dependency</th>
            <th class="py-2 px-4 border-b text-left text-sm font-semibold text-gray-600">ES</th>
            <th class="py-2 px-4 border-b text-left text-sm font-semibold text-gray-600">EF</th>
            <th class="py-2 px-4 border-b text-left text-sm font-semibold text-gray-600">LS</th>
            <th class="py-2 px-4 border-b text-left text-sm font-semibold text-gray-600">LF</th>
            <th class="py-2 px-4 border-b text-left text-sm font-semibold text-gray-600">Float</th>
            <th class="py-2 px-4 border-b text-left text-sm font-semibold text-gray-600">Critical?</th>
            <th class="py-2 px-4 border-b text-left text-sm font-semibold text-gray-600">Action</th>
          </tr>
        </thead>
        <tbody>
          <tr
            v-for="activity in activities"
            :key="activity.name"
            class="hover:bg-gray-50"
          >
            <td class="py-2 px-4 border-b text-gray-700">{{ activity.name }}</td>
            <td class="py-2 px-4 border-b text-gray-700">{{ activity.duration }}</td>
            <td class="py-2 px-4 border-b text-gray-700">{{ activity.unit }}</td>
            <td class="py-2 px-4 border-b text-gray-700">{{ activity.dependency }}</td>
            <td class="py-2 px-4 border-b text-gray-700">{{ activity.ES }}</td>
            <td class="py-2 px-4 border-b text-gray-700">{{ activity.EF }}</td>
            <td class="py-2 px-4 border-b text-gray-700">{{ activity.LS }}</td>
            <td class="py-2 px-4 border-b text-gray-700">{{ activity.LF }}</td>
            <td class="py-2 px-4 border-b text-gray-700">{{ activity.float }}</td>
            <td class="py-2 px-4 border-b text-gray-700">{{ activity.float === 0 ? "Yes" : "No" }}</td>
            <td class="py-2 px-4 border-b text-gray-700">
              <button
                class="bg-red-500 text-white px-2 py-1 rounded hover:bg-red-600"
                @click="removeActivity(activity.name)"
              >
                Delete
              </button>
            </td>
          </tr>
        </tbody>
      </table>

      <!-- Calculate Button -->
      <button
        @click="calculateCPM"
        class="mt-6 bg-green-600 text-white px-4 py-2 rounded-md hover:bg-green-700 focus:ring-2 focus:ring-green-500"
      >
        Calculate Critical Path
      </button>

      <button 
        class="mt-4 mx-2 px-4 py-2 bg-red-500 text-white rounded hover:bg-red-600" 
        @click="clearData">
        Clear Data
      </button>

    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      // Data baru untuk aktivitas yang akan ditambahkan
      newActivity: {
        name: "", // Nama aktivitas
        duration: 0, // Durasi aktivitas
        unit: "days", // Satuan durasi (days, weeks, months)
        dependency: "", // Ketergantungan pada aktivitas lain
      },
      // List aktivitas yang dimuat dari sessionStorage saat aplikasi dimulai
      activities: this.loadActivities(),
    };
  },
  methods: {
    // Tambahkan aktivitas baru ke daftar aktivitas dan simpan ke sessionStorage
    addActivity() {
      // Pisahkan string dependency menjadi array berdasarkan koma
      const dependencies = this.newActivity.dependency
        ? this.newActivity.dependency.split(",").map(dep => dep.trim())
        : [];

      // Konversi durasi ke hari (berdasarkan satuan yang dipilih)
      let durationInDays = this.convertToDays(this.newActivity.duration, this.newActivity.unit);

      // Tambahkan aktivitas baru ke daftar
      this.activities.push({
        name: this.newActivity.name,
        duration: durationInDays,
        unit: this.newActivity.unit,
        dependency: dependencies,
        ES: 0, // Earliest Start
        EF: 0, // Earliest Finish
        LS: 0, // Latest Start
        LF: 0, // Latest Finish
        float: 0, // Waktu float (kelonggaran)
      });

      // Simpan data aktivitas ke sessionStorage
      this.saveActivities();

      // Reset form input ke nilai awal
      this.newActivity = { name: "", duration: 0, unit: "days", dependency: "" };
    },

    removeActivity(activityName) {
      this.activities = this.activities.filter(activity => activity.name !== activityName);
      this.saveActivities(); // Perbarui sessionStorage setelah penghapusan
    },

    // Konversi durasi aktivitas ke dalam satuan hari
    convertToDays(duration, unit) {
      if (unit === "weeks") return duration * 7; // 1 minggu = 7 hari
      if (unit === "months") return Math.round(duration * 30.44); // Rata-rata 30.44 hari per bulan
      return duration; // Jika satuannya hari, kembalikan nilai apa adanya
    },

    // Hitung Critical Path Method (CPM) untuk semua aktivitas
    calculateCPM() {
      // Hitung Earliest Start (ES) dan Earliest Finish (EF)
      this.activities.forEach(activity => {
        if (activity.dependency.length === 0) {
          // Jika tidak ada dependency, ES = 0
          activity.ES = 0;
        } else {
          // Jika ada dependency, ES adalah EF terbesar dari dependency
          const dependencyEFs = activity.dependency.map(depName => {
            const depActivity = this.activities.find(a => a.name === depName);
            return depActivity ? depActivity.EF : 0;
          });
          activity.ES = Math.max(...dependencyEFs);
        }
        // Hitung EF berdasarkan ES + durasi
        activity.EF = activity.ES + activity.duration;
      });

      // Hitung durasi proyek (EF terbesar dari semua aktivitas)
      const projectDuration = Math.max(...this.activities.map(a => a.EF));

      // Hitung Latest Finish (LF), Latest Start (LS), dan float
      [...this.activities].reverse().forEach(activity => {
        if (activity === this.activities[this.activities.length - 1]) {
          // Aktivitas terakhir memiliki LF sama dengan durasi proyek
          activity.LF = projectDuration;
        } else {
          // LF adalah LS terkecil dari aktivitas berikutnya yang bergantung pada aktivitas ini
          const nextActivities = this.activities.filter(a => a.dependency.includes(activity.name));
          const nextLSs = nextActivities.map(next => next.LS);
          activity.LF = nextLSs.length > 0 ? Math.min(...nextLSs) : projectDuration;
        }
        // Hitung LS berdasarkan LF - durasi
        activity.LS = activity.LF - activity.duration;
        // Hitung float (waktu kelonggaran)
        activity.float = activity.LS - activity.ES;
      });

      // Simpan hasil kalkulasi ke sessionStorage
      this.saveActivities();
    },

    // Bersihkan semua data aktivitas dari aplikasi dan sessionStorage
    clearData() {
      this.activities = []; // Kosongkan daftar aktivitas
      sessionStorage.removeItem("activities"); // Hapus data dari sessionStorage
    },

    // Muat data aktivitas dari sessionStorage
    loadActivities() {
      const cachedData = sessionStorage.getItem("activities");
      return cachedData ? JSON.parse(cachedData) : []; // Jika ada data di cache, parsing JSON-nya, jika tidak, kembalikan array kosong
    },

    // Simpan data aktivitas ke sessionStorage
    saveActivities() {
      sessionStorage.setItem("activities", JSON.stringify(this.activities));
    },
  },
};
</script>

<style>
body {
  font-family: 'Inter', sans-serif;
}
</style>