<script setup lang="ts">
import { ref, computed } from 'vue'

// 設定預設值
const expectedAge = ref(90)
const currentAge = ref(33.5) // 預設值改為小數點
const WEEKS_PER_YEAR = 52

// 計算已經度過與剩餘的年數
const passedYears = computed(() => Math.min(Number(currentAge.value), Number(expectedAge.value)))
const remainingYears = computed(() => Math.max(Number(expectedAge.value) - Number(currentAge.value), 0))

// 計算已度過與剩餘的週數
const passedWeeks = computed(() => Math.floor(passedYears.value * WEEKS_PER_YEAR))
const remainingWeeks = computed(() => Math.floor(remainingYears.value * WEEKS_PER_YEAR))

// 計算用於顯示的年份陣列
const lifeYears = computed(() => {
  const years = []
  const totalPassedWeeks = Math.floor(passedYears.value * WEEKS_PER_YEAR)
  const totalYears = Math.ceil(Number(expectedAge.value))
  
  // 建立每年的資料
  for (let i = 0; i < totalYears; i++) {
    const weeks = []
    for (let j = 0; j < WEEKS_PER_YEAR; j++) {
      const weekIndex = i * WEEKS_PER_YEAR + j
      weeks.push({ passed: weekIndex < totalPassedWeeks })
    }
    
    const yearPassed = i < Math.floor(passedYears.value) || 
                      (i === Math.floor(passedYears.value) && 
                       weeks.every(w => w.passed))
                       
    years.push({ 
      year: i + 1, 
      weeks, 
      passed: yearPassed
    })
  }
  
  return years
})
</script>

<template>
  <div class="container">
    <h1>生命格子計算器</h1>
    <div class="inputs">
      <div class="input-group">
        <label for="expected-age">預期壽命：</label>
        <input 
          id="expected-age" 
          type="number" 
          v-model="expectedAge" 
          min="0.1" 
          max="150"
          step="0.1"
        />
      </div>
      <div class="input-group">
        <label for="current-age">目前年齡：</label>
        <input 
          id="current-age" 
          type="number" 
          v-model="currentAge" 
          min="0" 
          :max="expectedAge"
          step="0.1"
        />
      </div>
    </div>
    
    <div class="stats">
      <div class="stat-item">
        <span class="stat-label">已經度過：</span>
        <span class="stat-value">{{ passedYears.toFixed(1) }} 年 ({{ passedWeeks }} 週)</span>
      </div>
      <div class="stat-item">
        <span class="stat-label">剩餘時間：</span>
        <span class="stat-value">{{ remainingYears.toFixed(1) }} 年 ({{ remainingWeeks }} 週)</span>
      </div>
    </div>

    <div class="life-container">
      <div v-for="year in lifeYears" :key="year.year" class="year-row">
        <div class="year-label" :class="{ 'passed-year': year.passed }">
          {{ year.year }}年
        </div>
        <div class="weeks-container">
          <div 
            v-for="(week, weekIndex) in year.weeks" 
            :key="weekIndex"
            class="life-box"
            :class="{ 'passed': week.passed }"
            :title="`第 ${year.year} 年 第 ${weekIndex + 1} 週`"
          ></div>
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped>
.container {
  max-width: 1200px;
  margin: 0 auto;
  padding: 20px;
  font-family: Arial, sans-serif;
}

h1 {
  text-align: center;
  margin-bottom: 30px;
}

.inputs {
  display: flex;
  justify-content: center;
  gap: 20px;
  margin-bottom: 30px;
}

.input-group {
  display: flex;
  flex-direction: column;
}

label {
  margin-bottom: 5px;
  font-weight: bold;
}

input {
  padding: 8px;
  border: 1px solid #ccc;
  border-radius: 4px;
  width: 100px;
}

.stats {
  display: flex;
  justify-content: center;
  gap: 40px;
  margin-bottom: 30px;
}

.stat-item {
  text-align: center;
}

.stat-label {
  font-weight: bold;
  margin-right: 5px;
}

.stat-value {
  font-size: 18px;
}

.life-container {
  display: flex;
  flex-direction: column;
  gap: 4px;
}

.year-row {
  display: flex;
  align-items: center;
  gap: 10px;
}

.year-label {
  width: 50px;
  text-align: right;
  font-size: 12px;
  color: #666;
}

.passed-year {
  font-weight: bold;
  color: #333;
}

.weeks-container {
  display: flex;
  flex-direction: row;
  flex-wrap: nowrap;
  gap: 2px;
}

.life-box {
  width: 10px;
  height: 10px;
  border: 1px solid #ccc;
  border-radius: 2px;
}

.passed {
  background-color: #333;
  border: 1px solid #333;
}
</style>
