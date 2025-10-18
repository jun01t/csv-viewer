<template>
  <div class="app">
    <header class="header">
      <div class="container">
        <h1 class="title">
          <svg class="title-icon" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24"
            stroke="currentColor">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2"
              d="M9 12h6m-6 4h6m2 5H7a2 2 0 01-2-2V5a2 2 0 012-2h5.586a1 1 0 01.707.293l5.414 5.414a1 1 0 01.293.707V19a2 2 0 01-2 2z" />
          </svg>
          CSV ビューワー
        </h1>
        <p class="subtitle">CSVファイルを簡単に閲覧できます</p>
      </div>
    </header>

    <main class="main">
      <div class="container">
        <CsvUploader @csv-parsed="handleCsvParsed" />
        <CsvTable :csv-data="csvData" :file-name="fileName" />
      </div>
    </main>

    <footer class="footer">
      <div class="container">
        <p class="footer-text">
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

<style>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

body {
  font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, 'Helvetica Neue', Arial, sans-serif;
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  min-height: 100vh;
}

#app {
  min-height: 100vh;
}

.app {
  min-height: 100vh;
  display: flex;
  flex-direction: column;
}

.container {
  max-width: 1200px;
  margin: 0 auto;
  padding: 0 1rem;
  width: 100%;
}

.header {
  background-color: rgba(255, 255, 255, 0.1);
  backdrop-filter: blur(10px);
  padding: 2rem 0;
  box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
}

.title {
  display: flex;
  align-items: center;
  gap: 1rem;
  font-size: 2.5rem;
  font-weight: 700;
  color: white;
  margin-bottom: 0.5rem;
}

.title-icon {
  width: 48px;
  height: 48px;
}

.subtitle {
  font-size: 1.125rem;
  color: rgba(255, 255, 255, 0.9);
  font-weight: 300;
}

.main {
  flex: 1;
  padding: 3rem 0;
}

.footer {
  background-color: rgba(0, 0, 0, 0.2);
  padding: 1.5rem 0;
  margin-top: auto;
}

.footer-text {
  text-align: center;
  color: white;
  font-size: 0.875rem;
}

@media (max-width: 768px) {
  .title {
    font-size: 2rem;
  }

  .title-icon {
    width: 36px;
    height: 36px;
  }

  .subtitle {
    font-size: 1rem;
  }

  .main {
    padding: 2rem 0;
  }
}
</style>
