<script setup>
import { reactive } from "vue";

const emit = defineEmits(["create-todo"]);

const todoState = reactive({
  todo: "",
  invalid: null,
  errMsg: ""
});

const createTodo = () => {
  todoState.invalid = null;

  if (todoState.todo === "") {
    todoState.invalid = true;
    todoState.errMsg = "Todo cannot be empty";
    return;
  }

  emit("create-todo", todoState.todo);
  todoState.todo = "";
}

</script>

<template>
  <div class="input-wrap" :class="{ 'input-err': todoState.invalid }">
    <input type="text" v-model="todoState.todo" />
    <Button @click="createTodo()">Create</Button>
  </div>
  <p class="err-msg" v-show="todoState.invalid">{{ todoState.errMsg }}</p>
</template>

<style lang="scss" scoped>
.input-wrap {
  display: flex;
  transition: 250ms ease;
  border: 2px solid #ff8080;

  &.input-err {
    border-color: red;
  }

  &:focus-within {
    box-shadow: 0 -4px 6px -1px rgb(0 0 0 / 0.1),
      0 -2px 4px -2px rgb(0 0 0 / 0.1);
  }

  input {
    width: 100%;
    padding: 8px 6px;
    border: none;

    &:focus {
      outline: none;
    }
  }
}

button {
  padding: 8px 16px;
  border: none;
}

.err-msg {
  margin-top: 6px;
  font-size: 12px;
  color: red;
}
</style>