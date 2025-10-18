<template>
    <div class="w-full mt-8">
        <div v-if="csvData && csvData.length > 0" class="bg-white rounded-xl shadow-lg overflow-hidden">
            <div class="flex justify-around p-6 bg-gradient-to-r from-indigo-500 via-purple-500 to-pink-500 text-white">
                <div class="flex flex-col items-center gap-1">
                    <span class="text-sm opacity-90">ファイル名:</span>
                    <span class="text-2xl font-bold">{{ fileName }}</span>
                </div>
                <div class="flex flex-col items-center gap-1">
                    <span class="text-sm opacity-90">行数:</span>
                    <span class="text-2xl font-bold">{{ csvData.length }}</span>
                </div>
                <div class="flex flex-col items-center gap-1">
                    <span class="text-sm opacity-90">列数:</span>
                    <span class="text-2xl font-bold">{{ columns.length }}</span>
                </div>
            </div>

            <div class="overflow-x-auto overflow-y-auto max-h-[600px]">
                <table class="w-full border-collapse text-sm">
                    <thead class="bg-gray-700 text-white sticky top-0 z-10">
                        <tr>
                            <th
                                class="bg-gray-800 px-4 py-4 text-center font-semibold border-b-2 border-gray-900 whitespace-nowrap sticky left-0 z-[15]">
                                #</th>
                            <th v-for="column in columns" :key="column"
                                class="px-4 py-4 text-left font-semibold border-b-2 border-gray-900 whitespace-nowrap">
                                {{ column }}
                            </th>
                        </tr>
                    </thead>
                    <tbody>
                        <tr v-for="(row, index) in csvData" :key="index" class="transition-colors duration-200"
                            :class="index % 2 === 0 ? 'bg-gray-50 hover:bg-blue-50' : 'bg-white hover:bg-blue-50'">
                            <td
                                class="px-4 py-3 text-center font-semibold text-gray-700 bg-gray-100 sticky left-0 z-[5]">
                                {{ index + 1 }}
                            </td>
                            <td v-for="column in columns" :key="column"
                                class="px-4 py-3 border-b border-gray-200 text-gray-800">
                                {{ row[column] }}
                            </td>
                        </tr>
                    </tbody>
                </table>
            </div>
        </div>

        <div v-else class="flex flex-col items-center justify-center p-16 bg-white rounded-xl shadow-lg">
            <svg class="w-20 h-20 text-gray-300 mb-4" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24"
                stroke="currentColor">
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2"
                    d="M9 12h6m-6 4h6m2 5H7a2 2 0 01-2-2V5a2 2 0 012-2h5.586a1 1 0 01.707.293l5.414 5.414a1 1 0 01.293.707V19a2 2 0 01-2 2z" />
            </svg>
            <p class="text-lg text-gray-400">CSVファイルをアップロードしてください</p>
        </div>
    </div>
</template>

<script setup>
import { computed } from 'vue';

const props = defineProps({
    csvData: {
        type: Array,
        default: () => []
    },
    fileName: {
        type: String,
        default: ''
    }
});

const columns = computed(() => {
    if (props.csvData && props.csvData.length > 0) {
        return Object.keys(props.csvData[0]);
    }
    return [];
});
</script>
