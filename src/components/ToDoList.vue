<template>
    <div class="todo-app">
        <h1 class="title">Liste de tâches</h1>
        <div class="input-group">
            <input v-model="newTask" class="task-input" placeholder="Ajouter une tâche" />
            <button @click="addTask" class="add-btn">Ajouter</button>
        </div>

        <ul class="task-list">
            <li v-for="task in tasks" :key="task.id" class="task-item">
                <span :class="{ completed: task.completed }" class="task-title" @click="toggleComplete(task)">
                    {{ task.title }}
                </span>
                <button @click="removeTask(task.id)" class="remove-btn">
                    Supprimer
                </button>
            </li>
        </ul>
    </div>
</template>

<script lang="ts">
import { defineComponent, ref, onMounted } from "vue";
import axios from "axios";

interface Task {
    id: number;
    title: string;
    completed: boolean;
}

export default defineComponent({
    name: "ToDoList",
    setup() {
        const tasks = ref<Task[]>([]);
        const newTask = ref<string>("");
        const apiUrl = "https://jsonplaceholder.typicode.com/todos";

        const fetchTasks = async () => {
            try {
                const response = await axios.get(apiUrl);
                tasks.value = response.data.slice(0, 10);
            } catch (error) {
                console.error("Erreur lors de la récupération des tâches", error);
            }
        };

        const addTask = async () => {
            if (newTask.value.trim() === "") return;

            const taskToAdd: Task = {
                id: Date.now(),
                title: newTask.value,
                completed: false,
            };

            try {
                await axios.post(apiUrl, taskToAdd);
                tasks.value.push(taskToAdd);
                newTask.value = "";
            } catch (error) {
                console.error("Erreur lors de l'ajout de la tâche", error);
            }
        };

        const toggleComplete = async (task: Task) => {
            task.completed = !task.completed;
            try {
                await axios.put(`${apiUrl}/${task.id}`, task);
            } catch (error) {
                console.error("Erreur lors de la mise à jour de la tâche", error);
            }
        };

        const removeTask = async (taskId: number) => {
            try {
                await axios.delete(`${apiUrl}/${taskId}`);
                tasks.value = tasks.value.filter(task => task.id !== taskId);
            } catch (error) {
                console.error("Erreur lors de la suppression de la tâche", error);
            }
        };

        onMounted(fetchTasks);

        return {
            tasks,
            newTask,
            addTask,
            toggleComplete,
            removeTask,
        };
    },
});
</script>
