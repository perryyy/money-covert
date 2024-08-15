<template>
  <el-card>
    <div>
      <h2>Convert</h2>
      <div class="title">
        <el-text class="mx-1" size="large">韓元轉台幣</el-text>
        <el-input v-model.number="exchangeRate" class="exchangeRate" style="width: 180px;margin-left: 2%;" placeholder="輸入匯率" type="number" />
      </div>
    </div>
    <el-divider />
    <div style="display: flex">
      <el-button type="primary" plain @click="addRow">Add</el-button>
      <el-button type="info" plain @click="resetRows">Reset</el-button>
      <el-button type="success" plain @click="save">Save</el-button>
    </div>
    <el-scrollbar height="400px" style="margin-top: 3%">
      <div v-for="(row, index) in rows" :key="index" class="row">
        <el-input v-model.number="row.krw" style="width: 35%" @input="convertCurrency(index, 'krw')" placeholder="KRW" type="number"/>
        <el-icon><Right /></el-icon>
        <el-input v-model.number="row.twd" style="width: 35%" :value="row.twd.toFixed(2)" placeholder="TWD" type="number" readonly/>
        <el-button type="danger" :icon="Delete" plain style="margin-left: 2%" @click="removeRow(index)" />
      </div>
    </el-scrollbar>
    <el-divider />
    <div>
      <h2>Total</h2>
      <div style="display: flex;align-items: center;justify-content: space-around;">
        <el-input v-model="totalKrw" style="width: 45%" placeholder="Total KRW" readonly/>
        <el-icon><Right /></el-icon>
        <el-input :value="totalTwd.toFixed(2)" style="width: 45%" placeholder="Total TWD" readonly/>
      </div>
    </div>
  </el-card>
</template>

<script setup>
import { reactive, ref, computed, onMounted } from 'vue'
import { Delete, Refresh } from '@element-plus/icons-vue'

const exchangeRate = ref(36.5)
const rows = reactive([{ krw: 0, twd: 0 }])

onMounted(() => {
  const storedData = window.localStorage.getItem('currencyData')
  if (storedData) {
    const { exchangeRate: savedExchangeRate, rows: savedRows } = JSON.parse(storedData)
    exchangeRate.value = savedExchangeRate
    rows.splice(0, rows.length, ...savedRows)
  }
})

const addRow = () => {
  rows.push({ krw: 0, twd: 0 })
}

const removeRow = (index) => {
  rows.splice(index, 1)
}

const resetRows = () => {
  rows.splice(0, rows.length, { krw: 0, twd: 0 })
}

const resetExchangeRate = () => {
  exchangeRate.value = 36.5
}

const convertCurrency = (index, type) => {
  const row = rows[index]
  if (type === 'krw') {
    row.twd = parseFloat((row.krw / exchangeRate.value).toFixed(2))
  }
}

const totalKrw = computed(() => {
  return rows.reduce((sum, row) => sum + row.krw, 0)
})

const totalTwd = computed(() => {
  return rows.reduce((sum, row) => sum + parseFloat(row.twd), 0)
})

const save = () => {
  const data = {
    exchangeRate: exchangeRate.value,
    rows: JSON.parse(JSON.stringify(rows))
  }
  window.localStorage.setItem('currencyData', JSON.stringify(data))
}
</script>

<style scoped>
.row {
  display: flex;
  align-items: center;
  margin-bottom: 10px;
  flex-wrap: wrap;
}
.title {
  display: flex;
}
</style>
