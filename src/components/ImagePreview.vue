<template>
  <div class="p-4">
    <h3 class="text-semibold text-center mb-2">Image Preview</h3>
    <img class="p-1 bg-white border rounded" :src="preview" />
  </div>
</template>

<script setup>
//Component nay khong can thiet lam
const props = defineProps({
  preview: {
    type: String,
    required: true,
  },
});
</script>

<style></style>
