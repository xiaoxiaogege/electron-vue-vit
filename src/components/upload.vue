<template>
  <a-upload
    v-model:file-list="fileList"
    name="avatar"
    :maxCount="1"
    list-type="picture-card"
    class="avatar-uploader"
    :show-upload-list="false"
    :before-upload="beforeUpload"
    @change="handleChange"
  >
    <img
      v-if="imageUrl"
      :src="imageUrl"
      alt="avatar"
      width="300"
      height="200"
    />
    <div v-else>
      <loading-outlined v-if="loading"></loading-outlined>
      <plus-outlined v-else></plus-outlined>
      <div class="ant-upload-text">Upload</div>
    </div>
  </a-upload>
</template>
<script lang="ts" setup>
import { ref } from "vue";
import { PlusOutlined, LoadingOutlined } from "@ant-design/icons-vue";
import { message } from "ant-design-vue";
import type { UploadChangeParam, UploadProps } from "ant-design-vue";

const emits = defineEmits(["uploaded"]);

function getBase64(img: any, callback: (base64Url: string) => void) {
  const reader = new FileReader();
  reader.addEventListener("load", () => callback(reader.result as string));
  reader.readAsDataURL(img);
}

const fileList = ref([]);
const loading = ref<boolean>(false);
const imageUrl = ref<string>("");

const handleChange = (info: UploadChangeParam) => {
  console.log(info);
  getBase64(info.file.originFileObj, (base64Url: string) => {
    imageUrl.value = base64Url;
    loading.value = false;
  });
  emits("uploaded", imageUrl.value);
  //   if (info.file.status === "uploading") {
  //     loading.value = true;
  //     return;
  //   }
  //   if (info.file.status === "done") {
  //     // Get this url from response in real world.
  //     getBase64(info.file.originFileObj, (base64Url: string) => {
  //       imageUrl.value = base64Url;
  //       loading.value = false;
  //     });
  //   }
  //   if (info.file.status === "error") {
  //     loading.value = false;
  //     message.error("upload error");
  //   }
};

const beforeUpload = (file: any) => {
  const isJpgOrPng = file.type === "image/jpeg" || file.type === "image/png";
  if (!isJpgOrPng) {
    message.error("You can only upload JPG file!");
  }
  const isLt2M = file.size / 1024 / 1024 < 2;
  if (!isLt2M) {
    message.error("Image must smaller than 2MB!");
  }
  return isJpgOrPng && isLt2M;
};
</script>
<style scoped>
.avatar-uploader > .ant-upload {
  width: 128px;
  height: 128px;
}
.ant-upload-select-picture-card i {
  font-size: 32px;
  color: #999;
}

.ant-upload-select-picture-card .ant-upload-text {
  margin-top: 8px;
  color: #666;
}
</style>
