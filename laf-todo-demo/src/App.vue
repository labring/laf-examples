<script setup>
import { ref } from "vue";
import { Cloud } from "laf-client-sdk"; // 引入

// 创建cloud对象
const cloud = new Cloud({
  baseUrl: "https://<AppID>.laf.dev", // 这里的 <AppID> 需要换成自己的 AppID
  getAccessToken: () => "", // 这里先为空
});

const newTodo = ref("");
const list = ref([]);

// ===============================function===============================
getList();
async function getList() {
  const res = await cloud.invoke("get-list");
  list.value = res.data;
}

async function submit() {
  if (!newTodo.value) return;

  const obj = {
    name: newTodo.value,
    complete: false,
  };

  await cloud.invoke("add-todo", obj);
  newTodo.value = "";

  getList();
}

async function complete(index, id) {
  list.value[index].complete = !list.value[index].complete;
  await cloud.invoke("update-todo", {
    id,
    data: list.value[index],
  });
}

async function del(id) {
  await cloud.invoke("del-todo", { id });
  getList();
}
</script>

<template>
  <div class="todo-app">
    <h1>Todo App</h1>

    <div class="todo-form">
      <input type="text" v-model="newTodo" class="todo-input" placeholder="Add a todo" />
      <button class="todo-button" @click="submit">Add Todo</button>
    </div>

    <ul v-for="(item, index) in list">
      <li :class="[item.complete ? 'completed todo-row' : 'todo-row']">
        <div class="item">
          <div>
            <input
              type="checkbox"
              :checked="item.complete"
              @click="complete(index, item._id)"
            />
            <span>{{ item.name }}</span>
          </div>
          <div class="del" @click="del(item._id)">del</div>
        </div>
      </li>
    </ul>
  </div>
</template>

<style>
* {
  font-family: "Open Sans";
}
body {
  background: linear-gradient(
    90deg,
    rgba(105, 20, 204, 1) 0%,
    rgba(44, 114, 251, 1) 100%
  );
}
.todo-app {
  background: #ffffff;
  display: flex;
  flex-direction: column;
  justify-content: flex-start;
  min-height: 500px;
  padding: 0 20px;
  margin: 0 20px;
  margin-top: 40px;
  justify-items: center;
  text-align: center;
  border-radius: 5px;
}

h1 {
  margin: 32px 0;
  font-size: 24px;
}

.completed {
  text-decoration: line-through;
  opacity: 0.4;
}
.item {
  display: flex;
  width: 100%;
  justify-content: space-between;
}
.del {
  color: red;
  cursor: pointer;
}
.todo-form {
  display: flex;
  margin-bottom: 32px;
}

.todo-button {
  padding: 16px;
  border: none;
  border-radius: 0 20px 20px 0;
  cursor: pointer;
  outline: none;
  background: linear-gradient(
    90deg,
    rgba(105, 20, 204, 1) 0%,
    rgba(44, 114, 251, 1) 100%
  );
  color: #fff;
  text-transform: capitalize;
}

.todo-input {
  padding: 15px 32px 15px 16px;
  border-radius: 20px 0 0 20px;
  border: 1px solid #dfe1e5;
  outline: none;
  width: 60%;
  background: transparent;
}

.todo-input.edit {
  border: 2px solid #149fff;
}

.todo-button.edit {
  background: linear-gradient(
    90deg,
    rgba(20, 159, 255, 1) 0%,
    rgba(17, 122, 255, 1) 100%
  );
  padding: 16px 22px;
}

.todo-container {
  display: flex;
  flex-direction: row;
  position: relative;
}

ul {
  list-style-type: none;
  padding: 0;
  margin: 0;
}

.todo-row {
  display: flex;
  justify-content: space-between;
  align-items: center;
  box-shadow: rgba(149, 157, 165, 0.2) 0px 8px 24px;
  margin: 4px auto;

  padding: 16px;
  border-radius: 20px;
  width: 80%;
}

.todo-row input {
  margin-right: 10px;
}
</style>
