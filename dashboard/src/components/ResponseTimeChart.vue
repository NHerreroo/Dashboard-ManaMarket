<template>
    <div class="chart-container">
      <h3>{{ title }}</h3>
      <div class="kpi-value">{{ currentValue }}<span class="unit">ms</span></div>
      <div class="response-time-bars">
        <div 
          v-for="(value, i) in data" 
          :key="i" 
          class="response-time-bar"
          :style="{ height: `${value / maxValue * 100}%` }"
        ></div>
      </div>
    </div>
  </template>
  
  <script>
  export default {
    name: 'ResponseTimeChart',
    props: {
      title: {
        type: String,
        default: 'Tiempo medio de respuesta'
      },
      data: {
        type: Array,
        required: true
      },
      currentValue: {
        type: Number,
        required: true
      },
      maxValue: {
        type: Number,
        default: 300
      }
    }
  }
  </script>
  
  <style scoped>
  .chart-container {
    width: 100%;
    height: 100%;
    display: flex;
    flex-direction: column;
    overflow: hidden;
  }
  
  h3 {
    margin-top: 0;
    margin-bottom: 10px;
    font-size: 16px;
    font-weight: 500;
    color: #ccc;
  }
  
  .kpi-value {
    font-size: 28px;
    font-weight: 700;
    margin-bottom: 15px;
    color: #fff;
  }
  
  .unit {
    font-size: 16px;
    font-weight: 400;
    color: #aaa;
    margin-left: 2px;
  }
  
  .response-time-bars {
    flex: 1;
    display: flex;
    align-items: flex-end;
    gap: 6px;
    height: 150px;
    max-height: 150px;
    overflow: hidden;
  }
  
  .response-time-bar {
    flex: 1;
    background-color: #F08A24;
    border-radius: 4px 4px 0 0;
    min-height: 4px;
    transition: height 0.3s ease;
  }
  
  @media (min-width: 768px) {
    .response-time-bars {
      height: 200px;
      max-height: 200px;
    }
  }
  </style>