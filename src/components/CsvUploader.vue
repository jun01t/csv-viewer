<template>
    <div class="w-full">
        <div class="border-2 border-dashed rounded-xl p-12 text-center transition-all duration-300 cursor-pointer"
            :class="isDragging
                ? 'border-green-500 bg-green-50 scale-[1.02]'
                : 'border-gray-300 bg-gray-50 hover:border-green-500 hover:bg-green-50'" @drop.prevent="handleDrop"
            @dragover.prevent @dragenter.prevent="isDragging = true" @dragleave.prevent="isDragging = false">
            <div class="flex flex-col items-center gap-4">
                <svg class="w-16 h-16 text-green-500" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24"
                    stroke="currentColor">
                    <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2"
                        d="M7 16a4 4 0 01-.88-7.903A5 5 0 1115.9 6L16 6a5 5 0 011 9.9M15 13l-3-3m0 0l-3 3m3-3v12" />
                </svg>
                <p class="text-lg font-semibold text-gray-800">ドラッグ&ドロップでCSVファイルをアップロード</p>
                <p class="text-sm text-gray-600">または</p>
                <label class="cursor-pointer">
                    <input type="file" ref="fileInput" @change="handleFileSelect" accept=".csv" class="hidden" />
                    <span
                        class="inline-block px-6 py-3 bg-green-500 text-white font-semibold rounded-lg transition-all duration-300 hover:bg-green-600 hover:-translate-y-0.5 hover:shadow-lg">
                        ファイルを選択
                    </span>
                </label>
            </div>
        </div>

        <div v-if="fileName" class="flex items-center gap-3 mt-4 p-4 bg-gray-100 rounded-lg">
            <svg class="w-6 h-6 text-green-500 flex-shrink-0" xmlns="http://www.w3.org/2000/svg" fill="none"
                viewBox="0 0 24 24" stroke="currentColor">
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2"
                    d="M9 12h6m-6 4h6m2 5H7a2 2 0 01-2-2V5a2 2 0 012-2h5.586a1 1 0 01.707.293l5.414 5.414a1 1 0 01.293.707V19a2 2 0 01-2 2z" />
            </svg>
            <span class="flex-1 font-medium text-gray-800">{{ fileName }}</span>
            <button @click="clearFile" class="p-1 text-gray-500 hover:text-red-500 transition-colors duration-200">
                <svg class="w-5 h-5" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24"
                    stroke="currentColor">
                    <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12" />
                </svg>
            </button>
        </div>
    </div>
</template>

<script setup lang="ts">
import { ref } from 'vue';
import Papa from 'papaparse';
import Encoding from 'encoding-japanese';

type CsvParsedPayload = { data: any[]; fileName: string } | null;
const emit = defineEmits<{ 'csv-parsed': [payload: CsvParsedPayload] }>();

const isDragging = ref<boolean>(false);
const fileName = ref<string>('');
const fileInput = ref<HTMLInputElement | null>(null);

const parseFile = (file: File | null | undefined) => {
    if (file && file.type === 'text/csv') {
        fileName.value = file.name;

        // FileReaderを使用してファイルを読み込む
        const reader = new FileReader();

        reader.onload = (e: ProgressEvent<FileReader>) => {
            try {
                const result = e.target?.result as ArrayBuffer;
                const codes = new Uint8Array(result);

                // エンコーディングを自動検出
                const detectedEncoding = (Encoding as any).detect(codes) as string | null;
                console.log('検出されたエンコーディング:', detectedEncoding);

                // UTF-8に変換
                const unicodeArray = (Encoding as any).convert(codes, {
                    to: 'UNICODE',
                    from: detectedEncoding || 'AUTO'
                });

                // UTF-8文字列に変換
                const csvText = (Encoding as any).codeToString(unicodeArray as any);

                // PapaParseでCSVをパース
                Papa.parse(csvText, {
                    complete: (results: Papa.ParseResult<any>) => {
                        emit('csv-parsed', {
                            data: results.data,
                            fileName: file.name
                        });
                    },
                    header: true,
                    skipEmptyLines: true
                } as Papa.ParseConfig<any>);
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

const handleDrop = (event: DragEvent) => {
    isDragging.value = false;
    const files = event.dataTransfer?.files as FileList;
    if (files.length > 0) {
        parseFile(files[0]);
    }
};

const handleFileSelect = (event: Event) => {
    const input = event.target as HTMLInputElement;
    const files = input.files as FileList;
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
