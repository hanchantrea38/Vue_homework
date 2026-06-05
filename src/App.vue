<!-- <template>
  <div class="app">
    <h1> Task Manager</h1>

    <div class="input-group">
      <input type="text" placeholder="Add task..." v-model="newTask" @keyup.enter="addTask">

      <select v-model="priority">
        <option value="Low">Low</option>
        <option value="Medium">Medium</option>
        <option value="High">High</option>
      </select>

      <button @click="addTask">
        Add
      </button>
    </div>

    <p class="count">
      {{ doneCount }} of {{ tasks.length }} done
    </p>


    <ul v-if="filteredTasks.length">
      <li v-for="task in filteredTasks" :key="task.id">
        <div class="left">
          <input type="checkbox" v-model="task.done">

          <span :class="{ done: task.done }">
            {{ task.title }}
          </span>

          <span class="badge" :class="{
            low: task.priority === 'Low',
            medium: task.priority === 'Medium',
            high: task.priority === 'High'
          }">
            {{ task.priority }}
          </span>
        </div>

        <button class="delete-btn" @click="deleteTask(task.id)">
          Delete
        </button>
      </li>
    </ul>

    <p v-else>No tasks yet!</p>

    <div class="filters">
      <button @click="filter = 'all'">
        All
      </button>

      <button @click="filter = 'active'">
        Active
      </button>

      <button @click="filter = 'done'">
        Done
      </button>
    </div>
  </div>
</template> -->

<!-- <script setup>
import { ref, computed } from 'vue'

const newTask = ref('')
const priority = ref('Low')
const filter = ref('all')

const tasks = ref([])

function addTask() {
  if (newTask.value.trim() === '') return

  tasks.value.push({
    id: Date.now(),
    title: newTask.value,
    priority: priority.value,
    done: false
  })

  newTask.value = ''
  priority.value = 'Low'
}

function deleteTask(id) {
  tasks.value = tasks.value.filter(task => task.id !== id)
}

const filteredTasks = computed(() => {
  if (filter.value === 'active') {
    return tasks.value.filter(task => !task.done)
  }

  if (filter.value === 'done') {
    return tasks.value.filter(task => task.done)
  }

  return tasks.value
})

const doneCount = computed(() => {
  return tasks.value.filter(task => task.done).length
})
</script> -->

<!-- <style>
body {
  background: #f3f4f6;
  font-family: Arial, sans-serif;
}

.app {
  width: 700px;
  margin: 40px auto;
  background: white;
  padding: 30px;
  border-radius: 20px;
  box-shadow: 0 10px 20px rgba(0, 0, 0, .1);
}

h1 {
  color: #4f46e5;
  text-align: center;
  margin-bottom: 25px;
}

.input-group {
  display: flex;
  gap: 10px;
}

input[type="text"] {
  flex: 1;
  padding: 12px;
  border: 2px solid #ddd;
  border-radius: 10px;
}

select {
  padding: 12px;
  border-radius: 10px;
}

button {
  border: none;
  cursor: pointer;
  padding: 12px 16px;
  border-radius: 10px;
  background: #4f46e5;
  color: white;
}

.count {
  margin-top: 20px;
  font-weight: bold;
  color: gray;
}

ul {
  list-style: none;
  margin-top: 20px;
}

li {
  display: flex;
  justify-content: space-between;
  align-items: center;
  background: #f9fafb;
  padding: 15px;
  margin-bottom: 15px;
  border-radius: 15px;
}

.left {
  display: flex;
  align-items: center;
  gap: 15px;
}

.done {
  text-decoration: line-through;
  color: gray;
}

.badge {
  padding: 5px 12px;
  border-radius: 20px;
  color: white;
  font-size: 14px;
}

.low {
  background: #22c55e;
}

.medium {
  background: #f59e0b;
}

.high {
  background: #ef4444;
}

.delete-btn {
  background: #dc2626;
}

.filters {
  text-align: center;
  margin-top: 20px;
}

.filters button {
  margin: 0 5px;
}
</style>  -->


<template>
  <div class="min-h-screen bg-gradient-to-br from-blue-50 to-indigo-100 p-8">
    <div class="max-w-2xl mx-auto">
      <!-- Header -->
      <div class="bg-white rounded-lg shadow-lg p-4 mb-6">
        <h1 class="text-2xl font-bold text-blue-600 text-center mb-1">Student Grade Calculator</h1>
      </div>

      <!-- Student Info Card -->
      <div class="bg-white rounded-lg shadow-lg p-8">
        <!-- Student Name -->
        <div class="m-2 border-blue-500 pl-4 py-2 bg-blue-50 rounded">
          <p class="text-gray-700">
            <span class="font-semibold text-gray-800">Student Name: {{ studentName }}</span>
          </p>
        </div>

        <!-- Scores Grid -->
        <div class="grid gap-4">
          <div class="bg-green-50 border border-green-200 rounded-lg p-4">
            <p class="text-gray-600 text-sm font-semibold">Average Score is: {{ average }}</p>
          </div>
          <div class="bg-purple-50 border border-purple-200 rounded-lg p-4">
            <p class="text-gray-600 text-sm font-semibold">Highest Score is: {{ highestScore }}</p>
          </div>
          <div class="bg-orange-50 border border-orange-200 rounded-lg p-4">
            <p class="text-gray-600 text-sm font-semibold">Lowest Score is: {{ lowestScore }}</p>
          </div>
        </div>

        <!-- Grade and Status -->
        <div class="grid gap-4">
          <div class="bg-green-50 border border-green-200 rounded-lg p-4">
            <p class="text-gray-600 text-sm font-semibold">Student's Grade is: {{ studentGrade }}</p>
          </div>
          <div :class="{
            'bg-gradient-to-r to-emerald-50 border-2 border-green-400': examStatus === 'Pass',
            'bg-gradient-to-r from-red-50 to-pink-50 border-2 border-red-400': examStatus === 'Fail'
          }" class="rounded-lg p-6">
            <p class="text-gray-600 text-sm font-semibold mb-">Exam Status is: {{ examStatus }}</p>
            <p :class="{
              'text-emerald-600': examStatus === 'Pass',
              'text-red-600': examStatus === 'Fail'
            }" class="text-4xl font-bold"></p>
          </div>
        </div>

        <!-- Summary -->
        <div class="bg-indigo-50 border-indigo-500 rounded-lg p-6">
          <p class="text-gray-700">
            <span class="font-semibold text-indigo-700">Summary: </span>
            <span class="text-indigo-600">{{ summary }}</span>
          </p>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, computed } from 'vue'

const studentName = ref('Chantrea')
const scores = ref([
  { subject: 'Eng', score: 75 },
  { subject: 'Math', score: 80 },
  { subject: 'Sci', score: 70 }
])
const passMark = ref(50)
const totalScore = computed(() => {
  let total = 0;
  scores.value.forEach(subject => {
    total += subject.score;
  });
  return total;
})
//
const average = computed(() => {
  return totalScore.value / scores.value.length;
})

//
const highestScore = computed(() => {
  let highest = scores.value[0].score;

  scores.value.forEach(subject => {
    if (subject.score > highest) {
      highest = subject.score;
    }
  });
  return highest;
})

//
const lowestScore = computed(() => {
  let lowest = scores.value[0].score;

  scores.value.forEach(subject => {
    if (subject.score < lowest) {
      lowest = subject.score;
    }
  });
  return lowest;
})

//
const studentGrade = computed(() => {
  const avg = average.value;
  if (avg >= 90) return 'A';
  if (avg >= 80) return 'B';
  if (avg >= 70) return 'C';
  if (avg >= 60) return 'D';
  return 'F';
})

//
const examStatus = computed(() => {
  return average.value >= passMark.value ? 'Pass' : 'Fail';
})

//
const summary = computed(() => {
  return `${studentName.value} scored ${average.value.toFixed(2)} and received grade ${studentGrade.value} (${examStatus.value})`;
})

</script>

<style></style>