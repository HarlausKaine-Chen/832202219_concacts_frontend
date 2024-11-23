<script setup>
import { ref } from "vue";
import axios from "axios";
import * as XLSX from "xlsx";
import "./style1.css";

const ID = ref("");
const PHONE = ref("");
const NAME = ref("");
const email = ref("");
const social_media_handles = ref("");
const physical_address = ref("");
const list = ref([]);
const important = ref("Not important");
const activePopup = ref(null); // Tracks which popup is active
const cacheInput = ref(""); // Temporary input value
const cachedInputs = ref({}); // Cache for each item's input
const currentInput = ref(""); // Temporary input value
const cachedNumbers = ref({}); // Stores numbers for each user by index
const inputValues = ref([]);
getList();

async function confirmInput(index) {
  cachedInputs.value[index] = cacheInput.value; // Cache the input for this index
  cacheInput.value = ""; // Clear input field
  activePopup.value = null; // Close popup
}

function addNumber(index) {
  const num = parseFloat(inputValues.value[index]); // Parse input as a number
  if (isNaN(num)) {
    alert("Please enter a valid number.");
    return;
  }
  if (!cachedNumbers.value[index]) {
    cachedNumbers.value[index] = []; // Initialize array if not present
  }
  cachedNumbers.value[index].push(num); // Add number to cache
  inputValues.value[index] = ""; // Clear input field
}

// 导出数据到 Excel 文件
async function handleExport() {
  // 将列表数据转换为工作表
  const worksheet = XLSX.utils.json_to_sheet(list.value);
  // 创建一个新的工作簿并将工作表添加到其中
  const workbook = XLSX.utils.book_new();
  XLSX.utils.book_append_sheet(workbook, worksheet, "用户数据");
  // 导出 Excel 文件
  XLSX.writeFile(workbook, "users.xlsx");
}

function openPopup(index) {
  if (!cachedNumbers.value[index]) {
    cachedNumbers.value[index] = []; // Create an empty array if not initialized
  }
}

// 导入 Excel 文件
async function handleImport(event) {
  // 从文件输入中获取文件
  const file = event.target.files[0];
  if (!file) return;

  // 使用 FileReader 读取文件内容
  const reader = new FileReader();
  reader.onload = (e) => {
    const data = new Uint8Array(e.target.result);
    const workbook = XLSX.read(data, { type: "array" });

    // 假设数据在第一个工作表中
    const worksheet = workbook.Sheets[workbook.SheetNames[0]];
    // 将工作表数据转换为 JSON
    const jsonData = XLSX.utils.sheet_to_json(worksheet);

    // 更新列表数据
    list.value = jsonData;
  };
  reader.readAsArrayBuffer(file);
}

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
    email: email.value,
    smh: social_media_handles.value,
    pa: physical_address.value,
    important: important.value,
  });

  getList();
  ID.value = "";
  PHONE.value = "";
  NAME.value = "";
  email.value = "";
  social_media_handles.value = "";
  physical_address.value = "";
  important.value = "Not important";
}

async function update() {
  await axios.post("http://localhost:8081/user", {
    id: ID.value,
    password: PHONE.value,
    name: NAME.value,
    email: email.value,
    smh: social_media_handles.value,
    pa: physical_address.value,
    important: important.value,
  });
  getList();
  ID.value = "";
  PHONE.value = "";
  NAME.value = "";
  email.value = "";
  social_media_handles.value = "";
  physical_address.value = "";
  important.value = "Not important";
}

async function del() {
  await axios.delete(`http://localhost:8081/user/${ID.value}`, {});

  getList();

  ID.value = "";
}
async function SC() {
  important.value = "IT IS IMPORTANT!";
}
</script>

<template>
  <div class="APP">
    <div class="title">通讯录</div>
    <button @click="handleExport">导出数据</button>
    <input type="file" @change="handleImport" accept=".xlsx, .xls" />
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

    <div class="FORM">
      <input
        v-model="email"
        type="text"
        class="INPUT"
        placeholder="Add email"
      />
      <input
        v-model="social_media_handles"
        type="text"
        class="INPUT"
        placeholder="Add social media handles"
      />
    </div>

    <div class="FORM">
      <input
        v-model="physical_address"
        type="text"
        class="INPUT"
        placeholder="Add physical address"
      />
      <div @click="SC" class="BUTTON">important?</div>
    </div>

    <div>
      <table class="styled-table">
        <thead>
          <tr>
            <th>Information</th>
            <th>Importance</th>
            <th>Other phone number</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="(item, index) in list" :key="item.id">
            <td>
              <div class="content-box">
                <p><strong>ID:</strong> {{ item.id }}</p>
                <p><strong>Name:</strong> {{ item.name }}</p>
                <p><strong>Password:</strong> {{ item.password }}</p>
                <p><strong>Email:</strong> {{ item.email }}</p>
                <p><strong>Social Media Handles:</strong> {{ item.smh }}</p>
                <p><strong>Physical Address:</strong> {{ item.pa }}</p>
                <button @click="openPopup(index)" class="small-btn">
                  Edit
                </button>
              </div>
            </td>
            <td>{{ item.important }}</td>
            <td>
              <div class="number-section">
                <div class="input-section">
                  <input
                    v-model="inputValues[index]"
                    type="number"
                    class="number-input"
                    placeholder="Add number"
                  />
                  <button @click="addNumber(index)" class="add-btn">Add</button>
                </div>
                <div class="numbers-display">
                  <span
                    v-for="(num, i) in cachedNumbers[index]"
                    :key="i"
                    class="number-item"
                  >
                    {{ num }}
                  </span>
                </div>
              </div>
            </td>
          </tr>
        </tbody>
      </table>
    </div>
  </div>
</template>
