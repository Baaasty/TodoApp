<script setup>
import { ref, watch, computed, guardReactiveProps } from "vue";
import { uid } from "uid";
import { Icon } from "@iconify/vue";
import Draggable from "vuedraggable"

import TodoCreator from "../components/TodoCreator.vue";
import TodoItem from "../components/TodoItem.vue";

const todoList = ref([]);

watch(todoList, () => {
  setTodoList();
}, {
  deep: true
});

const todoCompleted = computed(() => {
  return todoList.value.every(todo => todo.isCompleted);
});

const fetchTodoList = () => {
  const todoListLocalStorage = localStorage.getItem("todoList");

  if (todoListLocalStorage) {
    todoList.value = JSON.parse(todoListLocalStorage);
  }
}

fetchTodoList();

const setTodoList = () => {
  localStorage.setItem("todoList", JSON.stringify(todoList.value));
}

const createTodo = (todo) => {
  todoList.value.push({
    id: uid(),
    todo,
    isCompleted: null,
    isEditing: null
  });
}

const toggleTodoComplete = (index) => {
  todoList.value[index].isCompleted = !todoList.value[index].isCompleted;
}

const toggleEditTodo = (index) => {
  todoList.value[index].isEditing = !todoList.value[index].isEditing;
}

const updateTodo = (todo, index) => {
  todoList.value[index].todo = todo;
}

const deleteTodo = (id) => {
  todoList.value = todoList.value.filter(todo => todo.id !== id);
}

const drag = ref(false);

const setDragCursor = (value) => {
  const html = document.getElementsByTagName('html').item(0);
  html.classList.toggle('grabbing', value);

  drag.value = value;
}
</script>

<template>
  <main>
    <h1>Create Todo</h1>
    <TodoCreator @create-todo="createTodo" />
    <Draggable 
      class="todo-list"
      v-model="todoList"
      tag="ul"
      :animation="150"
      :forceFallback="true"
      @start="setDragCursor(true)"
      @end="setDragCursor(false)"
      v-if="todoList.length > 0"
    >
      <template #item="{ element: todo, index }">
        <TodoItem :class="{ 'todo-item': drag }" :todo="todo" :index="index" @toggle-complete="toggleTodoComplete"
          @edit-todo="toggleEditTodo" @update-todo="updateTodo" @delete-todo="deleteTodo" />
      </template>
    </Draggable>
    <p class="todos-msg" v-else>
      <Icon icon="noto-v1:sad-but-relieved-face" width="22" />
      <span>You have no todo's to complete! Add one!</span>
    </p>
    <p class="todos-msg" v-if="todoCompleted && todoList.length > 0">
      <Icon icon="noto-v1:party-popper" width="22" />
      <span>You have completed all your todos!</span>
    </p>
  </main>
</template>

<style lang="scss">
#app .todo-list .todo-item input[type="checkbox"] {
  cursor: grabbing;
}

#app .todo-list .todo-item .todo-actions * {
  cursor: grabbing;
}

.grabbing * {
    cursor: grabbing !important;
}
</style>

<style scoped lang="scss">
main {
  display: flex;
  flex-direction: column;
  max-width: 500px;
  width: 100%;
  margin: 0 auto;
  padding: 40px 16px;

  h1 {
    margin-bottom: 16px;
    text-align: center;
  }

  .todo-list {
    display: flex;
    flex-direction: column;
    list-style: none;
    margin-top: 24px;
    gap: 20px;
  }

  .todos-msg {
    display: flex;
    align-items: center;
    justify-content: center;
    gap: 8px;
    margin-top: 24px;
  }
}
</style>
