<template>
    <div class="csv-table-container">
        <div v-if="csvData && csvData.length > 0" class="table-wrapper">
            <div class="table-info">
                <div class="info-item">
                    <span class="info-label">ファイル名:</span>
                    <span class="info-value">{{ fileName }}</span>
                </div>
                <div class="info-item">
                    <span class="info-label">行数:</span>
                    <span class="info-value">{{ csvData.length }}</span>
                </div>
                <div class="info-item">
                    <span class="info-label">列数:</span>
                    <span class="info-value">{{ columns.length }}</span>
                </div>
            </div>

            <div class="table-scroll">
                <table class="csv-table">
                    <thead>
                        <tr>
                            <th class="row-number">#</th>
                            <th v-for="column in columns" :key="column">
                                {{ column }}
                            </th>
                        </tr>
                    </thead>
                    <tbody>
                        <tr v-for="(row, index) in csvData" :key="index" :class="{ 'even-row': index % 2 === 0 }">
                            <td class="row-number">{{ index + 1 }}</td>
                            <td v-for="column in columns" :key="column">
                                {{ row[column] }}
                            </td>
                        </tr>
                    </tbody>
                </table>
            </div>
        </div>
        <div v-else class="empty-state">
            <svg class="empty-icon" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24"
                stroke="currentColor">
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2"
                    d="M9 12h6m-6 4h6m2 5H7a2 2 0 01-2-2V5a2 2 0 012-2h5.586a1 1 0 01.707.293l5.414 5.414a1 1 0 01.293.707V19a2 2 0 01-2 2z" />
            </svg>
            <p class="empty-text">CSVファイルをアップロードしてください</p>
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

<style scoped>
.csv-table-container {
    width: 100%;
    margin-top: 2rem;
}

.table-wrapper {
    background-color: white;
    border-radius: 12px;
    box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
    overflow: hidden;
}

.table-info {
    display: flex;
    justify-content: space-around;
    padding: 1.5rem;
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
    color: white;
}

.info-item {
    display: flex;
    flex-direction: column;
    align-items: center;
    gap: 0.25rem;
}

.info-label {
    font-size: 0.875rem;
    opacity: 0.9;
}

.info-value {
    font-size: 1.5rem;
    font-weight: 700;
}

.table-scroll {
    overflow-x: auto;
    max-height: 600px;
    overflow-y: auto;
}

.csv-table {
    width: 100%;
    border-collapse: collapse;
    font-size: 0.875rem;
}

.csv-table thead {
    background-color: #4a5568;
    color: white;
    position: sticky;
    top: 0;
    z-index: 10;
}

.csv-table th {
    padding: 1rem;
    text-align: left;
    font-weight: 600;
    border-bottom: 2px solid #2d3748;
    white-space: nowrap;
}

.csv-table td {
    padding: 0.75rem 1rem;
    border-bottom: 1px solid #e2e8f0;
    color: #2d3748;
}

.csv-table tbody tr {
    transition: background-color 0.2s ease;
}

.csv-table tbody tr:hover {
    background-color: #ebf8ff;
}

.csv-table tbody tr.even-row {
    background-color: #f7fafc;
}

.csv-table tbody tr.even-row:hover {
    background-color: #ebf8ff;
}

.row-number {
    background-color: #edf2f7;
    font-weight: 600;
    color: #4a5568;
    text-align: center;
    position: sticky;
    left: 0;
    z-index: 5;
}

.csv-table thead .row-number {
    background-color: #2d3748;
}

.empty-state {
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    padding: 4rem 2rem;
    background-color: white;
    border-radius: 12px;
    box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
}

.empty-icon {
    width: 80px;
    height: 80px;
    color: #cbd5e0;
    margin-bottom: 1rem;
}

.empty-text {
    font-size: 1.125rem;
    color: #a0aec0;
    margin: 0;
}
</style>
