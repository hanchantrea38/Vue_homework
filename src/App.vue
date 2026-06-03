<template>
  <div class="app">
    <h1> Task Manager</h1>

    <!-- Input -->
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

    <!-- Task List -->
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

    <!-- Filters -->
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
</template>



<script setup>
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
</script>

<style>
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
</style>
