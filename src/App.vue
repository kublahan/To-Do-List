<template>
  <div class="app">
    <h1>Мой To-Do List (TypeScript)</h1>
    <div class="input-container">
      <input
        v-model="newTask"
        @keyup.enter="addTask"
        placeholder="Добавьте задачу..."
      />
      <button @click="addTask">Добавить</button>
    </div>
    <ul v-if="tasks.length > 0">
      <li
        v-for="(task, index) in tasks"
        :key="index"
        :class="{ completed: task.completed }"
      >
        <span @click="toggleComplete(index)">{{ task.text }}</span>
        <button @click="removeTask(index)">❌</button>
      </li>
    </ul>
    <p v-else>Список задач пуст. Добавьте первую задачу!</p>
  </div>
</template>

<script lang="ts">
import { defineComponent, ref, onMounted } from 'vue';


interface Task {
  text: string;
  completed: boolean;
}

export default defineComponent({
  name: 'App',
  setup() {
    const newTask = ref<string>('');
    const tasks = ref<Task[]>([]);


    const loadTasks = (): void => {
      const savedTasks = localStorage.getItem('tasks');
      if (savedTasks) {
        tasks.value = JSON.parse(savedTasks);
      }
    };


    const saveTasks = (): void => {
      localStorage.setItem('tasks', JSON.stringify(tasks.value));
    };


    const addTask = (): void => {
      if (newTask.value.trim() === '') return;
      tasks.value.push({ text: newTask.value, completed: false });
      newTask.value = '';
      saveTasks();
    };


    const removeTask = (index: number): void => {
      tasks.value.splice(index, 1);
      saveTasks();
    };


    const toggleComplete = (index: number): void => {
      tasks.value[index].completed = !tasks.value[index].completed;
      saveTasks();
    };


    onMounted(() => {
      loadTasks();
    });

    return {
      newTask,
      tasks,
      addTask,
      removeTask,
      toggleComplete,
    };
  },
});
</script>

<style scoped>
.app {
  max-width: 500px;
  margin: 0 auto;
  padding: 20px;
  font-family: Arial, sans-serif;
}
.input-container {
  display: flex;
  gap: 10px;
  margin-bottom: 20px;
}
input {
  flex: 1;
  padding: 8px;
}
button {
  padding: 8px 12px;
  background: #42b983;
  color: white;
  border: none;
  cursor: pointer;
}
ul {
  list-style: none;
  padding: 0;
}
li {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 8px;
  border-bottom: 1px solid #eee;
}
li.completed span {
  text-decoration: line-through;
  color: #888;
}
</style>