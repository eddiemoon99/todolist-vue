<script setup>
import { ref, computed, onMounted, watch } from 'vue';

const todos = ref([]);
const name = ref('');
const inputContent = ref('');
const inputCategory = ref(null);

const todosSorted = computed(() =>
  todos.value.sort((a, b) => a.createdAt - b.createdAt)
);

const addTodo = () => {
  if (inputContent.value.trim() === '' || inputCategory.value === null) {
    return;
  }
  todos.value.push({
    content: inputContent.value,
    category: inputCategory.value,
    done: false,
    createdAt: new Date().getTime(),
  });
  inputContent.value = '';
  inputCategory.value = null;
};

const removeTodo = (todo) => {
  todos.value = todos.value.filter((t) => t !== todo);
};

watch(
  todos,
  (newTodos) => {
    localStorage.setItem('todos', JSON.stringify(newTodos));
  },
  { deep: true }
);

watch(name, (newName) => {
  localStorage.setItem('name', newName);
});

onMounted(() => {
  name.value = localStorage.getItem('name') || '';
  todos.value = JSON.parse(localStorage.getItem('todos')) || [];
});
</script>

<template>
  <main class="app">
    <section class="greeting">
      <h2 class="title">
        Hello
        <input type="text" placeholder="Enter your name here" v-model="name" />
      </h2>
    </section>
    <section class="create-todo">
      <h3>Create your todo</h3>
      <form @submit.prevent="addTodo">
        <h4>What's on your todo list?</h4>
        <input
          type="text"
          placeholder="e.g. finish laundry"
          v-model="inputContent"
        />
        <h4>Pick a category</h4>
        <div class="options">
          <label>
            <input
              type="radio"
              name="category"
              value="personal"
              v-model="inputCategory"
            />
            <span class="bubble personal"></span>
            <div>Personal</div>
          </label>
          <label>
            <input
              type="radio"
              name="category"
              value="business"
              v-model="inputCategory"
            />
            <span class="bubble business"></span>
            <div>Business</div>
          </label>
        </div>
        <input
          :style="{
            backgroundColor: '#124353',
          }"
          type="submit"
          value="Add todo"
        />
      </form>
    </section>

    <section class="todo-list">
      <h3>TODOs</h3>
      <div class="list">
        <div
          v-for="todo in todosSorted"
          :class="`todo-item ${todo.done && 'done'}`"
        >
          <label>
            <input type="checkbox" v-model="todo.done" />
            <span :class="`bubble ${todo.category}`"></span>
          </label>
          <div class="todo-content">
            <input type="text" v-model="todo.content" />
          </div>
          <div class="actions">
            <button class="delete" @click="removeTodo(todo)">Delete</button>
          </div>
        </div>
      </div>
    </section>
  </main>
</template>
