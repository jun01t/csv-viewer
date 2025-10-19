<template>
    <div class="w-full mt-8">
        <div v-if="csvData && csvData.length > 0" class="bg-white rounded-xl shadow-lg overflow-hidden">
            <div class="flex justify-around p-6 bg-gradient-to-r from-green-500 via-emerald-500 to-teal-500 text-white">
                <div class="flex flex-col items-center gap-1">
                    <span class="text-sm opacity-90">ファイル名:</span>
                    <span class="text-2xl font-bold">{{ fileName }}</span>
                </div>
                <div class="flex flex-col items-center gap-1">
                    <span class="text-sm opacity-90">行数:</span>
                    <span class="text-2xl font-bold">
                        {{ sortedData.length }}
                        <span v-if="filterText" class="text-base opacity-75">/ {{ csvData.length }}</span>
                    </span>
                </div>
                <div class="flex flex-col items-center gap-1">
                    <span class="text-sm opacity-90">列数:</span>
                    <span class="text-2xl font-bold">{{ columns.length }}</span>
                </div>
            </div>

            <!-- フィルター検索ボックス -->
            <div class="px-6 py-4 bg-gray-50 border-b border-gray-200">
                <div class="flex items-center gap-3">
                    <div class="flex-1 relative">
                        <svg class="absolute left-3 top-1/2 transform -translate-y-1/2 w-5 h-5 text-gray-400"
                            xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2"
                                d="M21 21l-6-6m2-5a7 7 0 11-14 0 7 7 0 0114 0z" />
                        </svg>
                        <input v-model="filterText" type="text" placeholder="全カラムから検索..."
                            class="w-full pl-10 pr-10 py-2 border border-gray-300 rounded-lg focus:ring-2 focus:ring-green-500 focus:border-transparent transition-all duration-200 text-gray-800" />
                        <button v-if="filterText" @click="clearFilter"
                            class="absolute right-3 top-1/2 transform -translate-y-1/2 text-gray-400 hover:text-gray-600 transition-colors duration-200">
                            <svg class="w-5 h-5" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24"
                                stroke="currentColor">
                                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2"
                                    d="M6 18L18 6M6 6l12 12" />
                            </svg>
                        </button>
                    </div>
                    <div v-if="filterText" class="text-sm text-gray-600 whitespace-nowrap">
                        {{ sortedData.length }} 件該当
                    </div>
                </div>
            </div>

            <div class="overflow-x-auto overflow-y-auto max-h-[600px]">
                <table class="w-full border-collapse text-sm">
                    <thead class="bg-gray-700 text-white sticky top-0 z-10">
                        <tr>
                            <th
                                class="bg-gray-800 px-4 py-4 text-center font-semibold border-b-2 border-gray-900 whitespace-nowrap sticky left-0 z-[15]">
                                #</th>
                            <th v-for="column in columns" :key="column" @click="handleSort(column)"
                                class="px-4 py-4 text-left font-semibold border-b-2 border-gray-900 whitespace-nowrap cursor-pointer hover:bg-gray-600 transition-colors duration-200">
                                <div class="flex items-center gap-2">
                                    <span>{{ column }}</span>
                                    <span class="flex flex-col">
                                        <svg v-if="sortColumn === column && sortDirection === 'asc'" class="w-4 h-4"
                                            xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24"
                                            stroke="currentColor">
                                            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2"
                                                d="M5 15l7-7 7 7" />
                                        </svg>
                                        <svg v-else-if="sortColumn === column && sortDirection === 'desc'"
                                            class="w-4 h-4" xmlns="http://www.w3.org/2000/svg" fill="none"
                                            viewBox="0 0 24 24" stroke="currentColor">
                                            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2"
                                                d="M19 9l-7 7-7-7" />
                                        </svg>
                                        <svg v-else class="w-4 h-4 opacity-30" xmlns="http://www.w3.org/2000/svg"
                                            fill="none" viewBox="0 0 24 24" stroke="currentColor">
                                            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2"
                                                d="M7 16V4m0 0L3 8m4-4l4 4m6 0v12m0 0l4-4m-4 4l-4-4" />
                                        </svg>
                                    </span>
                                </div>
                            </th>
                        </tr>
                    </thead>
                    <tbody>
                        <tr v-for="(row, index) in sortedData" :key="index" class="transition-colors duration-200"
                            :class="index % 2 === 0 ? 'bg-gray-50 hover:bg-green-50' : 'bg-white hover:bg-green-50'">
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
import { computed, ref } from 'vue';

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

const sortColumn = ref(null);
const sortDirection = ref('asc'); // 'asc' or 'desc'
const filterText = ref(''); // フィルター用のテキスト

const columns = computed(() => {
    if (props.csvData && props.csvData.length > 0) {
        return Object.keys(props.csvData[0]);
    }
    return [];
});

// フィルター処理
const filteredData = computed(() => {
    if (!filterText.value) {
        return props.csvData;
    }

    const searchText = filterText.value.toLowerCase();
    return props.csvData.filter(row => {
        // 全てのカラムを検索対象とする
        return columns.value.some(column => {
            const value = String(row[column]).toLowerCase();
            return value.includes(searchText);
        });
    });
});

// ソート処理（フィルター後のデータに対して実行）
const sortedData = computed(() => {
    const dataToSort = filteredData.value;

    if (!sortColumn.value) {
        return dataToSort;
    }

    const sorted = [...dataToSort].sort((a, b) => {
        const aValue = a[sortColumn.value];
        const bValue = b[sortColumn.value];

        // 数値として比較できる場合は数値として比較
        const aNum = parseFloat(aValue);
        const bNum = parseFloat(bValue);

        if (!isNaN(aNum) && !isNaN(bNum)) {
            return sortDirection.value === 'asc' ? aNum - bNum : bNum - aNum;
        }

        // 文字列として比較
        const aStr = String(aValue).toLowerCase();
        const bStr = String(bValue).toLowerCase();

        if (sortDirection.value === 'asc') {
            return aStr.localeCompare(bStr);
        } else {
            return bStr.localeCompare(aStr);
        }
    });

    return sorted;
});

const handleSort = (column) => {
    if (sortColumn.value === column) {
        // 同じカラムをクリックした場合は、ソート方向を切り替える
        sortDirection.value = sortDirection.value === 'asc' ? 'desc' : 'asc';
    } else {
        // 別のカラムをクリックした場合は、そのカラムで昇順ソート
        sortColumn.value = column;
        sortDirection.value = 'asc';
    }
};

const clearFilter = () => {
    filterText.value = '';
};
</script>
