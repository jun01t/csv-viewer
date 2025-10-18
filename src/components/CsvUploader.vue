<template>
    <div class="csv-uploader">
        <div class="upload-area" @drop.prevent="handleDrop" @dragover.prevent @dragenter.prevent="isDragging = true"
            @dragleave.prevent="isDragging = false" :class="{ 'dragging': isDragging }">
            <div class="upload-content">
                <svg class="upload-icon" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24"
                    stroke="currentColor">
                    <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2"
                        d="M7 16a4 4 0 01-.88-7.903A5 5 0 1115.9 6L16 6a5 5 0 011 9.9M15 13l-3-3m0 0l-3 3m3-3v12" />
                </svg>
                <p class="upload-text">ドラッグ&ドロップでCSVファイルをアップロード</p>
                <p class="upload-subtext">または</p>
                <label class="file-input-label">
                    <input type="file" ref="fileInput" @change="handleFileSelect" accept=".csv" class="file-input" />
                    <span class="file-button">ファイルを選択</span>
                </label>
            </div>
        </div>
        <div v-if="fileName" class="file-info">
            <svg class="file-icon" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24"
                stroke="currentColor">
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2"
                    d="M9 12h6m-6 4h6m2 5H7a2 2 0 01-2-2V5a2 2 0 012-2h5.586a1 1 0 01.707.293l5.414 5.414a1 1 0 01.293.707V19a2 2 0 01-2 2z" />
            </svg>
            <span class="file-name">{{ fileName }}</span>
            <button @click="clearFile" class="clear-button">
                <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                    <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12" />
                </svg>
            </button>
        </div>
    </div>
</template>

<script setup>
import { ref } from 'vue';
import Papa from 'papaparse';
import Encoding from 'encoding-japanese';

const emit = defineEmits(['csv-parsed']);

const isDragging = ref(false);
const fileName = ref('');
const fileInput = ref(null);

const parseFile = (file) => {
    if (file && file.type === 'text/csv') {
        fileName.value = file.name;

        // FileReaderを使用してファイルを読み込む
        const reader = new FileReader();

        reader.onload = (e) => {
            try {
                const codes = new Uint8Array(e.target.result);

                // エンコーディングを自動検出
                const detectedEncoding = Encoding.detect(codes);
                console.log('検出されたエンコーディング:', detectedEncoding);

                // UTF-8に変換
                const unicodeArray = Encoding.convert(codes, {
                    to: 'UNICODE',
                    from: detectedEncoding || 'AUTO'
                });

                // UTF-8文字列に変換
                const csvText = Encoding.codeToString(unicodeArray);

                // PapaParseでCSVをパース
                Papa.parse(csvText, {
                    complete: (results) => {
                        emit('csv-parsed', {
                            data: results.data,
                            fileName: file.name
                        });
                    },
                    header: true,
                    skipEmptyLines: true,
                    error: (error) => {
                        console.error('CSVのパースエラー:', error);
                        alert('CSVファイルの読み込みに失敗しました。');
                    }
                });
            } catch (error) {
                console.error('エンコーディング変換エラー:', error);
                alert('CSVファイルの読み込みに失敗しました。');
            }
        };

        reader.onerror = () => {
            alert('ファイルの読み込みに失敗しました。');
        };

        // ArrayBufferとして読み込む
        reader.readAsArrayBuffer(file);
    } else {
        alert('CSVファイルを選択してください。');
    }
};

const handleDrop = (event) => {
    isDragging.value = false;
    const files = event.dataTransfer.files;
    if (files.length > 0) {
        parseFile(files[0]);
    }
};

const handleFileSelect = (event) => {
    const files = event.target.files;
    if (files.length > 0) {
        parseFile(files[0]);
    }
};

const clearFile = () => {
    fileName.value = '';
    if (fileInput.value) {
        fileInput.value.value = '';
    }
    emit('csv-parsed', null);
};
</script>

<style scoped>
.csv-uploader {
    width: 100%;
}

.upload-area {
    border: 2px dashed #cbd5e0;
    border-radius: 12px;
    padding: 3rem 2rem;
    text-align: center;
    transition: all 0.3s ease;
    background-color: #f7fafc;
    cursor: pointer;
}

.upload-area:hover {
    border-color: #4299e1;
    background-color: #ebf8ff;
}

.upload-area.dragging {
    border-color: #4299e1;
    background-color: #ebf8ff;
    transform: scale(1.02);
}

.upload-content {
    display: flex;
    flex-direction: column;
    align-items: center;
    gap: 1rem;
}

.upload-icon {
    width: 64px;
    height: 64px;
    color: #4299e1;
}

.upload-text {
    font-size: 1.125rem;
    font-weight: 600;
    color: #2d3748;
    margin: 0;
}

.upload-subtext {
    font-size: 0.875rem;
    color: #718096;
    margin: 0;
}

.file-input-label {
    cursor: pointer;
}

.file-input {
    display: none;
}

.file-button {
    display: inline-block;
    padding: 0.75rem 1.5rem;
    background-color: #4299e1;
    color: white;
    font-weight: 600;
    border-radius: 8px;
    transition: all 0.3s ease;
    cursor: pointer;
}

.file-button:hover {
    background-color: #3182ce;
    transform: translateY(-2px);
    box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
}

.file-info {
    display: flex;
    align-items: center;
    gap: 0.75rem;
    margin-top: 1rem;
    padding: 1rem;
    background-color: #edf2f7;
    border-radius: 8px;
}

.file-icon {
    width: 24px;
    height: 24px;
    color: #4299e1;
    flex-shrink: 0;
}

.file-name {
    flex: 1;
    font-weight: 500;
    color: #2d3748;
}

.clear-button {
    background: none;
    border: none;
    cursor: pointer;
    padding: 0.25rem;
    color: #718096;
    transition: color 0.2s ease;
}

.clear-button:hover {
    color: #e53e3e;
}

.clear-button svg {
    width: 20px;
    height: 20px;
}
</style>
