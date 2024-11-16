<template>
    <div class="task-list">
      <h2 style="margin-top:0">To-Do List</h2>
      <input v-model="newTaskTitle" placeholder="Enter task title" @keyup.enter="addTask" style="padding: 5px;" />
      <select v-model="newTaskPriority" style="padding: 5px;">
        <option value="low">Low</option>
        <option value="medium">Medium</option>
        <option value="high">High</option>
      </select>
      <button @click="addTask" class="addTaskButton">Add Task</button>
  
      <transition-group name="list" tag="ul">
        <li v-for="task in sortedTasks" :key="task.id" :class="taskClass(task)">
            <div class="flex">
                <span :class="{ completed: task.completed }" class="taskName">
                    {{ task.title }}
                </span>
                <input type="checkbox" v-model="task.completed" />
                <button @click="deleteTask(task.id)">Delete</button>
            </div>
        </li>
      </transition-group>
  
      <p>Pending tasks: {{ pendingTasks }}</p>
    </div>
</template>
  
<script lang="ts" setup>
import { ref, computed, nextTick } from 'vue';
import { Task } from '../interfaces/Task';

const tasks = ref<Task[]>([]);
const newTaskTitle = ref('');
const newTaskPriority = ref<'low' | 'medium' | 'high'>('low');
let taskId = 1;

const addTask = async () => {
    if (newTaskTitle.value.trim() === '') return;
    const newTask: Task = {
        id: taskId++,
        title: newTaskTitle.value,
        priority: newTaskPriority.value,
        completed: false,
    };
    tasks.value.push(newTask);
    newTaskTitle.value = '';
};

const deleteTask = (id: number) => {
    tasks.value = tasks.value.filter((task) => task.id !== id);
};

const pendingTasks = computed(() => tasks.value.filter((task) => !task.completed).length);

const sortedTasks = computed(() =>
    [...tasks.value].sort((a, b) => {
        const priorityOrder = { low: 1, medium: 2, high: 3 };
        return priorityOrder[b.priority] - priorityOrder[a.priority];
    })
);

function taskClass(task: Task){
    if(task.priority === 'high') {
        return 'task-high';
    }  else if(task.priority === 'medium') {
        return 'task-medium';
    } else if(task.completed) {
        return 'completed';
    }
};

</script>
  
<style>
.task-list {
    padding-top: 10px;
    width: 300px;
    margin: auto;
    text-align: center;
}

.completed {
    text-decoration: line-through;
    color: gray;
}

.task-high {
    color: red;
}

.task-medium {
    color: orange;
}

.list-enter-active,
.list-leave-active {
    transition: all 0.3s ease;
}

.list-enter-from,
.list-leave-to {
    opacity: 0;
    transform: translateY(20px);
}

.addTaskButton{
    padding: 5px;
    margin: 5px;
    background-color: #4CAF50;
    color: white;
    border: none;
    cursor: pointer;
}

.flex{
    display: flex;
    justify-content: space-between;
    align-items: center;
    padding: 5px;
    border-bottom: 1px solid #ccc;
}

.taskName{
    min-width: 200px;
    max-width: 200px;
    line-break: anywhere;
}

ul{
    max-height: 300px;
    overflow-y: auto;
    width: 100%;
}
</style>