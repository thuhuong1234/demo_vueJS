<template>
  <header class="todo-header">
    <h2 class="todo-title">Todo List</h2>
  </header>
  <main class="todo-main">
    <div class="todo-main-left">
      <h3 class="todo-title">Todo List</h3>

      <p class="status-item">Completed: {{ completedTodos }}</p>

      <p class="status-item">Not Completed: {{ notCompletedTodos }}</p>
    </div>
    <div class="todo-main-right">
      <form @submit.prevent="addTodo(todoInput)">
        <input
          ref="todoInputRef"
          type="text"
          class="todo-input"
          placeholder="Enter todo ..."
          v-model="todoInput"
        />
        <input
          type="button"
          value="Add"
          class="todo-add"
          @click="addTodo(todoInput)"
          :disabled="todoInput === ''"
        />
      </form>
      <ul class="todo-list">
        <li v-for="todo in todoLists" :key="todo.id" class="todo-item">
          <input
            type="checkbox"
            :checked="todo.completed"
            @click="handleCompleteTodo(todo, !todo.completed)"
          />
          <span :class="{ '-done': todo.completed }">{{ todo.title }}</span>
          <input
            type="button"
            value="Delete"
            class="todo-delete"
            @click="deleteTodo(todo)"
          />
        </li>
      </ul>
    </div>
  </main>
</template>

<script setup>
import { ref, computed, onMounted } from "vue";

const todoLists = ref([]);

const deleteTodo = async (todo) => {
  const response = await fetch(
    `https://jsonplaceholder.typicode.com/todos/${todo.id}`,
    {
      method: "DELETE",
    }
  );
  if (response.ok) {
    todoLists.value = todoLists.value.filter((item) => item.id !== todo.id);
  }
};

const handleCompleteTodo = async (todo, completed) => {
  const response = await fetch(
    `https://jsonplaceholder.typicode.com/todos/${todo.id}`,
    {
      method: "PUT",
      body: JSON.stringify({
        completed,
      }),
      headers: {
        "Content-type": "application/json; charset=UTF-8",
      },
    }
  );

  if (response.ok) {
    todoLists.value = todoLists.value.map((item) => {
      if (item.id === todo.id) {
        return { ...item, completed };
      }
      return item;
    });
  }
};

const todoInput = ref("");

const addTodo = async (title) => {
  if (title === "") {
    return;
  }
  const newTodo = {
    title,
    completed: false,
  };
  const response = await fetch("https://jsonplaceholder.typicode.com/todos", {
    method: "POST",
    body: JSON.stringify({
      ...newTodo,
    }),
    headers: {
      "Content-type": "application/json; charset=UTF-8",
    },
  });
  const { id } = await response.json();
  if (response.ok) {
    todoInput.value = "";

    todoLists.value.unshift({ id, ...newTodo });
  }
};

const completedTodos = computed(() => {
  return todoLists.value.filter((todo) => todo.completed).length;
});

const notCompletedTodos = computed(() => {
  return todoLists.value.filter((todo) => !todo.completed).length;
});
const handleFetchTodos = async () => {
  const response = await fetch("https://jsonplaceholder.typicode.com/todos");
  const data = await response.json();
  todoLists.value = data;
  console.log(todoLists.value);
};
handleFetchTodos();
const todoInputRef = ref("");
onMounted(() => {
  todoInputRef.value.focus();
});
</script>

<style scoped>
.todo-title {
  display: flex;
  justify-content: center;
  align-items: center;
  height: 40px;
  font-size: 24px;
  font-weight: bold;
}
.todo-main {
  display: flex;
  margin: 20px;
  justify-content: space-between;
  width: 100%;
  padding: 20px;
}

.todo-main-left {
  width: 20%;
  margin-right: 10px;
}

.todo-main-right {
  width: 70%;
}
.todo-list {
  list-style: none;
  padding: 0;
  margin: 0;
}
.todo-item {
  display: flex;
  align-items: center;
  margin-bottom: 10px;
  padding: 10px;
  border-radius: 5px;
  background-color: #f8f9fa;
  border: 1px solid #dee2e6;
  font-size: 16px;
  font-weight: bold;
  color: #212529;
  cursor: pointer;
  gap: 10px;
}
.-done {
  color: #09c519;
}
.todo-delete {
  border: 1px solid #dee2e6;
  border-radius: 5px;
  padding: 5px 10px;
  background-color: #f8f9fa;
  cursor: pointer;
  font-size: 16px;
  font-weight: bold;
  color: #ff0000;
}
.todo-delete:hover {
  background-color: #ff0000;
  color: #f8f9fa;
}
.todo-input {
  border: 1px solid #dee2e6;
  border-radius: 5px;
  padding: 5px 10px;
  font-size: 16px;
  font-weight: bold;
  color: #212529;
  margin-bottom: 10px;
}
.todo-add {
  border: 1px solid #dee2e6;
  border-radius: 5px;
  padding: 5px 10px;
  background-color: #f8f9fa;
  cursor: pointer;
  font-size: 16px;
  font-weight: bold;
  color: #09c519;
}
.todo-add:hover {
  color: #f8f9fa;
  background-color: #09c519;
}
.todo-main-left {
  font-weight: bold;
  background-color: #7bb6f1;
  border: 1px solid #dee2e6;
  border-radius: 5px;
  padding: 0px;
  font-size: 24px;
  font-weight: bold;
  color: #212529;
}
.todo-title {
  font-weight: bold;
  color: #7bb6f1;
  border: 0px;
  border-radius: 5px;
  padding: 10px;
  margin-top: 0px;
  font-size: 24px;
  font-weight: bold;
  background-color: #dee2e6;
}
.status-item {
  font-weight: bold;
  color: #000000;
  border: 0px;
  border-radius: 5px;
  padding: 10px;
  margin-top: 0px;
  font-size: 20px;
  font-weight: bold;
}
.status-color {
  border: 1px;
  width: 10px;
  height: 10px;
  border-radius: 5px;
  padding: 5px 10px;
  background-color: #09c519;
}
</style>
