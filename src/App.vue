<template>
  <div class="app">
    <h1>Список задач</h1>
    <ul>
      <li v-for="task in tasks" :key="task.id">
        <input
            type="checkbox"
            :checked="task.done"
            @change="toggleTask(task)"
        />
        <span :style="{ 'text-decoration': task.done ? 'line-through' : 'none' }">
          {{ task.title }}
        </span>
      </li>
    </ul>
  </div>
</template>

<script>
export default {
  name: "App",
  data() {
    return {
      tasks: [],
    };
  },
  created() {
    this.loadTasks();
  },
  methods: {
    async loadTasks() {
      // 1) Проверяем, есть ли данные в localStorage
      const storedTasks = localStorage.getItem("tasks");
      if (storedTasks) {
        // Если в localStorage что-то есть, берём оттуда
        this.tasks = JSON.parse(storedTasks);
      } else {
        // Если localStorage пуст, загружаем из tasks.json
        try {
          const response = await fetch("/tasks.json");
          const data = await response.json();
          this.tasks = data;
          this.saveTasks();
        } catch (error) {
          console.error("Ошибка при загрузке tasks.json:", error);
        }
      }
    },
    saveTasks() {
      localStorage.setItem("tasks", JSON.stringify(this.tasks));
    },
    toggleTask(task) {
      // Переключаем флаг done
      task.done = !task.done;
      // Сохраняем обновление
      this.saveTasks();
    },
  },
};
</script>

<style>
.app {
  max-width: 400px;
  margin: 0 auto;
  font-family: sans-serif;
}
li {
  list-style: none;
}
</style>
