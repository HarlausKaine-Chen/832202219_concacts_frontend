<script setup>
import { ref } from "vue";
import axios from "axios";

const ID = ref("");
const PHONE = ref("");
const NAME = ref("");
const list = ref([]);

getList();

async function getList() {
  const res = await axios.get("http://localhost:8081/user");
  list.value = res.data;
  console.log(res);
}

async function add() {
  await axios.post("http://localhost:8081/user", {
    id: ID.value,
    password: PHONE.value,
    name: NAME.value,
  });

  getList();
  ID.value = "";
  PHONE.value = "";
  NAME.value = "";
}

async function update() {
  await axios.post("http://localhost:8081/user", {
    id: ID.value,
    password: PHONE.value,
    name: NAME.value,
  });
  getList();
  ID.value = "";
  PHONE.value = "";
  NAME.value = "";
}

async function del() {
  await axios.delete(`http://localhost:8081/user/${ID.value}`, {});

  getList();

  ID.value = "";
}
</script>

<template>
  <div class="APP">
    <div class="title">通讯录</div>

    <div class="FORM">
      <input v-model="ID" type="text" class="INPUT" placeholder="Add an ID" />
      <div @click="update" class="BUTTON">Update</div>
      <input
        v-model="PHONE"
        type="text"
        class="INPUT"
        placeholder="Add a phonenumber"
      />
      <div @click="del" class="BUTTON">Del</div>
      <input
        v-model="NAME"
        type="text"
        class="INPUT"
        placeholder="Add a name"
      />
      <div @click="add" class="BUTTON">Add</div>
    </div>

    <div>
      <table>
        <thead>
          <tr>
            <th>ID</th>
            <th>Name</th>
            <th>Password</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="(item, index) in list" :key="item.id">
            <td>{{ item.id }}</td>
            <td>{{ item.name }}</td>
            <td>{{ item.password }}</td>
          </tr>
        </tbody>
      </table>
    </div>
  </div>
</template>

<style scoped>
.APP {
  box-sizing: border-box;
  margin-top: 40px;
  margin-left: 1%;
  padding-top: 30px;
  width: 98%;
  height: 500px;
  background: #ffffff;
  border-radius: 5px;
}

.title {
  text-align: center;
  font-size: 30px;
  font-weight: 700;
}

.FORM {
  display: flex;
  margin: 20px 0 30px 20px;
}

.BUTTON {
  width: 100px;
  height: 52px;
  border-radius: 0 20px 20px 0;

  text-align: center;
  background: linear-gradient(
    to right,
    rgb(113, 65, 168),
    rgba(44, 114, 251, 1)
  );
  color: #fff;
  line-height: 52px;
  cursor: pointer;
  font-size: 14px;
  user-select: none;
}

.INPUT {
  padding: 0px 15px 0px 15px;
  border-radius: 20px 0 0 20px;
  border: 1px solid #dfe1e5;
  outline: none;
  width: 60%;
  height: 50px;
}

.item {
  box-sizing: border-box;
  display: flex;
  align-items: center;
  justify-content: space-between;
  width: 80%;
  height: 50px;
  margin: 8px auto;
  padding: 16px;
  border-radius: 20px;
  box-shadow: rgba(149, 157, 165, 0.2) 0px 8px 20px;
}

.completed {
  box-sizing: border-box;
  display: flex;
  align-items: center;
  justify-content: space-between;
  width: 80%;
  height: 50px;
  margin: 8px auto;
  padding: 16px;
  border-radius: 20px;
  box-shadow: rgba(149, 157, 165, 0.2) 0px 8px 20px;
  text-decoration: line-through;
  opacity: 0.4;
}
</style>
