<template>
  <div class="min-h-screen flex flex-col bg-gradient-to-br from-indigo-500 via-purple-500 to-pink-500">
    <header class="bg-white/10 backdrop-blur-md shadow-lg">
      <div class="max-w-7xl mx-auto px-4 py-8">
        <h1 class="flex items-center gap-4 text-4xl md:text-5xl font-bold text-white mb-2">
          <svg class="w-12 h-12" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24"
            stroke="currentColor">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2"
              d="M9 12h6m-6 4h6m2 5H7a2 2 0 01-2-2V5a2 2 0 012-2h5.586a1 1 0 01.707.293l5.414 5.414a1 1 0 01.293.707V19a2 2 0 01-2 2z" />
          </svg>
          CSV ビューワー
        </h1>
        <p class="text-lg md:text-xl text-white/90 font-light">CSVファイルを簡単に閲覧できます</p>
      </div>
    </header>

    <main class="flex-1 py-12">
      <div class="max-w-7xl mx-auto px-4 w-full">
        <CsvUploader @csv-parsed="handleCsvParsed" />
        <CsvTable :csv-data="csvData" :file-name="fileName" />
      </div>
    </main>

    <footer class="bg-black/20 py-6 mt-auto">
      <div class="max-w-7xl mx-auto px-4">
        <p class="text-center text-white text-sm">
          Powered by Vite & Vue 3 | Built with ❤️
        </p>
      </div>
    </footer>
  </div>
</template>

<script setup>
import { ref } from 'vue';
import CsvUploader from './components/CsvUploader.vue';
import CsvTable from './components/CsvTable.vue';

const csvData = ref([]);
const fileName = ref('');

const handleCsvParsed = (result) => {
  if (result) {
    csvData.value = result.data;
    fileName.value = result.fileName;
  } else {
    csvData.value = [];
    fileName.value = '';
  }
};
</script>
