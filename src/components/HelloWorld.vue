<script setup lang="ts">
import { onMounted, ref } from "vue";
import axios from "axios";
import upload from "./upload.vue";
// import { ipcRenderer } from "electron";

defineProps<{ msg: string }>();

const accessToken = ""; // 百度云ocr accessToken
onMounted(() => {
  // getData();
});
const getData = async () => {
  const response = await axios.post(
    "https://aip.baidubce.com/rest/2.0/ocr/v1/idcard",
    {
      id_card_side: "front",
      image: idBase64.value,
    },
    {
      headers: {
        "Content-Type": "application/x-www-form-urlencoded",
      },
      params: {
        access_token: accessToken,
      },
    }
  );
  console.log(response.data);
  idInfoData.value = response.data.words_result;
  // {
  //   name:'', // 姓名
  //   nationality:'', // 民族
  //   address:'',// 住址
  //   citizenshipNumber:'',// 公民身份号码
  //   dateOfBirth:'',// 出生年月
  //   gender:''// 性别
  // }
};

const count = ref(0);
const idBase64 = ref("");
const idInfoData = ref({});

function save() {
  window.ipcRenderer.send(
    "create-text-file",
    `
    姓名：${idInfoData.name}
    民族：${idInfoData.nationality}
    `
  );
}
</script>

<template>
  <p class="read-the-docs">上传身份证</p>
  <upload @uploaded="(data) => (idBase64 = data)" />
  <a-button type="primary" @click="getData">获取身份证信息</a-button>
  <a-button type="primary" @click="save">保存到本地</a-button>
  <!-- {{ idInfoData }} -->
</template>

<style scoped>
.read-the-docs {
  color: #888;
}
</style>
