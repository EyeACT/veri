<script setup lang="ts">
import { ref } from "vue";
import { invoke } from "@tauri-apps/api/core";

const activeTab = ref("verify");
const selectedFolder = ref("");

async function selectFolder(event: Event) {
  console.log(event);
  const input = event.target as HTMLInputElement;
  if (!input.files || input.files.length === 0) {
    console.log("No files selected");
    return;
  }

  // Get the first file's path (which will be the folder path)
  const folderPath = input.files[0].webkitRelativePath.split("/")[0];
  console.log("Selected folder path:", folderPath);

  try {
    selectedFolder.value = await invoke("open_folder", {
      path: folderPath,
    });
    console.log("Folder path from backend:", selectedFolder.value);
  } catch (error) {
    console.error("Error opening folder:", error);
  }
}
</script>

<template>
  <div class="flex h-screen w-screen overflow-hidden">
    <!-- Sidebar -->
    <div class="w-64 bg-gray-800 text-white p-5 flex flex-col fixed h-full">
      <div class="mb-10">
        <h2 class="text-2xl font-bold text-cyan-400">Veri</h2>
      </div>
      <div class="flex flex-col gap-2">
        <button
          class="px-5 py-3 text-left rounded-md transition-colors"
          :class="[
            activeTab === 'verify'
              ? 'bg-cyan-500 text-gray-800'
              : 'hover:bg-gray-700',
          ]"
          @click="activeTab = 'verify'"
        >
          Verify
        </button>
        <button
          class="px-5 py-3 text-left rounded-md opacity-50 cursor-not-allowed"
          :class="[
            activeTab === 'upload'
              ? 'bg-cyan-500 text-gray-800'
              : 'hover:bg-gray-700',
          ]"
          disabled
        >
          Upload
        </button>
      </div>
    </div>

    <!-- Main Content -->
    <div
      class="flex-1 ml-64 p-10 bg-gray-100 dark:bg-gray-900 text-gray-900 dark:text-gray-100 overflow-y-auto"
    >
      <div v-if="activeTab === 'verify'" class="max-w-3xl mx-auto">
        <h1 class="text-3xl font-bold mb-10">Verify Folder Structure</h1>
        <div class="text-center">
          <input
            type="file"
            @change="selectFolder"
            webkitdirectory
            directory
            multiple
            class="hidden"
            id="folderInput"
          />
          <label
            for="folderInput"
            class="px-6 py-3 bg-cyan-500 text-white rounded-md hover:bg-cyan-600 transition-colors cursor-pointer inline-block"
          >
            Select Folder
          </label>

          <p
            v-if="selectedFolder"
            class="mt-5 p-3 bg-gray-200 dark:bg-gray-700 rounded-md break-all"
          >
            Selected: {{ selectedFolder }}
          </p>
        </div>
      </div>
      <div v-else-if="activeTab === 'upload'" class="max-w-3xl mx-auto">
        <h1 class="text-3xl font-bold mb-5">Upload (Coming Soon)</h1>
        <p class="text-gray-600 dark:text-gray-400">
          This feature will be available in a future update.
        </p>
      </div>
    </div>
  </div>
</template>
