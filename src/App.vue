<template>
  <div id="app">
    <h1>📋 Task Manager</h1>

    <form @submit.prevent="addTask" class="task-form">
      <input v-model="newTask.title" placeholder="Task Title" required />
      <input v-model="newTask.description" placeholder="Description" />
      <select v-model="newTask.status">
        <option value="Pending">Pending</option>
        <option value="Completed">Completed</option>
      </select>
      <button type="submit" class="btn add-btn">➕ Add Task</button>
    </form>

    <ul class="task-list">
      <li v-for="task in tasks" :key="task._id" :class="task.status.toLowerCase()">
        <div>
          <strong>{{ task.title }}</strong> - {{ task.description }}
          <span class="status">[{{ task.status }}]</span>
        </div>
        <div>
          <button @click="editTask(task)" class="btn edit-btn">✏️ Edit</button>
          <button @click="deleteTask(task._id)" class="btn delete-btn">🗑 Delete</button>
        </div>
      </li>
    </ul>
  </div>
</template>

<script>
import axios from "axios";

export default {
  name: "App",
  data() {
    return {
      API_BASE: "https://task-manager-backend-1m7u.onrender.com", 
      tasks: [],
      newTask: {
        title: "",
        description: "",
        status: "Pending",
      },
    };
  },
  methods: {
    async getTasks() {
      const res = await axios.get(`${this.API_BASE}/tasks`);
      this.tasks = res.data;
    },
    async addTask() {
      await axios.post(`${this.API_BASE}/tasks`, this.newTask);
      this.newTask = { title: "", description: "", status: "Pending" };
      this.getTasks();
    },
    async editTask(task) {
      const updatedTitle = prompt("Edit title", task.title);
      if (updatedTitle) {
        await axios.put(`${this.API_BASE}/tasks/${task._id}`, {
          ...task,
          title: updatedTitle,
        });
        this.getTasks();
      }
    },
    async deleteTask(id) {
      await axios.delete(`${this.API_BASE}/tasks/${id}`);
      this.getTasks();
    },
  },
  mounted() {
    this.getTasks();
  },
};
</script>

<style>
body {
  font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
  background-color: #f4f6f8;
  margin: 0;
  padding: 0;
}

#app {
  max-width: 700px;
  margin: 30px auto;
  background: white;
  padding: 20px 30px;
  border-radius: 12px;
  box-shadow: 0px 4px 12px rgba(0,0,0,0.1);
}

h1 {
  text-align: center;
  color: #3f51b5;
  margin-bottom: 20px;
}

.task-form {
  display: flex;
  gap: 10px;
  flex-wrap: wrap;
  margin-bottom: 20px;
}

.task-form input, 
.task-form select {
  padding: 8px;
  border-radius: 6px;
  border: 1px solid #ccc;
  flex: 1;
}

.btn {
  padding: 8px 14px;
  border-radius: 6px;
  border: none;
  cursor: pointer;
  font-weight: bold;
  transition: 0.3s;
}

.add-btn {
  background-color: #4caf50;
  color: white;
}

.add-btn:hover {
  background-color: #45a049;
}

.edit-btn {
  background-color: #ff9800;
  color: white;
}

.edit-btn:hover {
  background-color: #e68900;
}

.delete-btn {
  background-color: #f44336;
  color: white;
}

.delete-btn:hover {
  background-color: #d32f2f;
}

.task-list {
  list-style: none;
  padding: 0;
}

.task-list li {
  display: flex;
  justify-content: space-between;
  align-items: center;
  background: #fafafa;
  padding: 10px;
  margin-bottom: 8px;
  border-radius: 6px;
  border-left: 5px solid transparent;
}

.task-list li.pending {
  border-left-color: #ff9800;
}

.task-list li.completed {
  border-left-color: #4caf50;
  text-decoration: line-through;
  opacity: 0.8;
}

.status {
  font-size: 0.9em;
  color: #666;
}
</style>
