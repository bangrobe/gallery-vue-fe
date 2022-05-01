<template>
  <div class="container px-8 mb-4">
    <h2 class="text-2xl font-semibold text-center">
      Upload an Image to Cloudinary
    </h2>
    <div class="grid md:grid-cols-6 grid-cols-1">
      <div class="md:col-start-2 md:col-span-4 shadow-lg">
        <div class="grid md:grid-cols-2 grid-cols-1">
          <div class="block p-6 rounded-lg bg-white max-w-sm mt-4">
            <form>
              <div class="form-group mb-6">
                <div>
                  <label
                    class="form-label inline-block mb-2 text-gray-700"
                    for="file-input"
                  >
                    Upload Image
                  </label>
                  <input
                    id="file-input"
                    type="file"
                    class="form-control block w-full px-3 py-1.5 text-base font-normal text-gray-700 border border-solid border-gray-300 rounded transition ease-in-out"
                    accept="image/png, image/jpg, image/jpeg"
                    @change="handleFileChange($event)"
                  />
                </div>
              </div>
              <button
                class="w-full px-6 py-2.5 bg-green-600 text-white font-medium text-xs leading-tight rounded shadow-md transition duration-150 ease-in-out focus:bg-green-200"
                :class="uploadStatus && 'bg-gray-600'"
                type="submit"
                :disabled="uploadStatus"
                @click.prevent="submitUpload"
              >
                {{ uploadStatus ? "Uploading" : "Upload" }}
              </button>
            </form>
          </div>
          <!--Display Image preview-->
          <div class="p-4">
            <h3 class="text-semibold text-center mb-2">Image Preview</h3>
            <AlertMessage v-if="error" :message="error" />
            <img
              v-if="filePreview"
              class="p-1 bg-white border rounded"
              :src="filePreview"
            />
            <p v-if="fileSize" class="text-semibold">{{ fileSize }}</p>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { useRouter } from "vue-router";
import axios from "axios";
import { ref } from "vue";
import AlertMessage from "./AlertMessage.vue";
import formatBytes from "@/utils/formatBytes";
const file = ref(null);
const filePreview = ref(null);
const fileSize = ref(0.0);
const router = useRouter();
const uploadStatus = ref(false);
const error = ref(null);

const API_URL = import.meta.env.VITE_API_URL;
const handleFileChange = (e) => {
  error.value = null;
  file.value = e.target.files[0];
  getImagePreviews(file.value);
};

//Convert to human-readable format size

const getImagePreviews = (image) => {
  //Iterate over all of the files and generate image preview for each one
  console.log(image.size);
  if (/\.(jpe?g|png|gif)$/i.test(image.name) && image.size < 1000000) {
    //Create a FileReader object
    let reader = new FileReader();

    reader.onloadend = (e) => {
      filePreview.value = e.target.result;
      fileSize.value = formatBytes(image.size);
    };
    reader.readAsDataURL(image);
  } else {
    error.value = "File is not supported or file size bigger than 1MB";
    filePreview.value = null;
    fileSize.value = null;
  }
};

const submitUpload = () => {
  //Create FileReader
  if (!file.value) return;
  let reader = new FileReader();
  reader.readAsDataURL(file.value);
  reader.onloadend = () => {
    uploadImage(reader.result);
  };
};

const uploadImage = async (file) => {
  uploadStatus.value = true;
  const formData = new FormData();
  if (file) formData.append("file", file);

  try {
    const response = await axios({
      method: "POST",
      url: `${API_URL}/api/upload`,
      data: formData,
      headers: {
        "Content-Type": "multipart/form-data",
      },
    });
    if (response.data) {
      error.value = null;
      uploadStatus.value = false;
      router.push("/");
    }
  } catch (error) {
    error.value = error;
    uploadStatus.value = false;
  }
};
</script>

<style></style>
