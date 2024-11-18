<template>
  <div class="min-h-screen bg-gray-100 py-8 px-4">
    <div class="container mx-auto bg-white shadow-md rounded-lg p-6">
      <h1 class="text-2xl font-bold text-gray-800 mb-6">CPM Project Scheduler</h1>

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
      newActivity: {
        name: "",
        duration: 0,
        unit: "days",
        dependency: "",
      },
      activities: [],
    };
  },
  methods: {
    addActivity() {
        let durationInDays = this.convertToDays(this.newActivity.duration, this.newActivity.unit);
        this.activities.push({
            name: this.newActivity.name,
            duration: durationInDays,
            unit: this.newActivity.unit,
            dependency: this.newActivity.dependency,
            ES: 0,
            EF: 0,
            LS: 0,
            LF: 0,
            float: 0
        });
        this.newActivity = { name: "", duration: 0, unit: "days", dependency: "" };
    },
    convertToDays(duration, unit) {
        if (unit === "weeks") return duration * 7;
        if (unit === "months") return duration * 30;
        return duration;
    },
    calculateCPM() {
        this.activities.forEach(activity => {
            if (!activity.dependency) {
                activity.ES = 0;
            } else {
                const depActivity = this.activities.find(a => a.name === activity.dependency);
                activity.ES = depActivity ? depActivity.EF : 0;
            }
            activity.EF = activity.ES + activity.duration;
        });

        const projectDuration = Math.max(...this.activities.map(a => a.EF));
        [...this.activities].reverse().forEach(activity => {
            if (activity === this.activities[this.activities.length - 1]) {
                activity.LF = projectDuration;
            } else {
                const nextActivity = this.activities.find(a => a.dependency === activity.name);
                activity.LF = nextActivity ? nextActivity.LS : projectDuration;
            }
            activity.LS = activity.LF - activity.duration;
            activity.float = activity.LS - activity.ES;
        });
    },
    clearData() {
        this.activities = [];
    }
  }
};
</script>

<style>
body {
  font-family: 'Inter', sans-serif;
}
</style>
