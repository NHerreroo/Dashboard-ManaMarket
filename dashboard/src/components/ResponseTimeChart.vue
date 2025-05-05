<template>
  <div class="chart-container">
    <h3>{{ title }}</h3>
    <div class="kpi-value">{{ currentValue }}<span class="unit">{{ unit }}</span></div>
    <div ref="chartContainer" class="echarts-container"></div>
  </div>
</template>

<script>
import { defineComponent, ref, onMounted, onUnmounted, watch } from 'vue';
import * as echarts from 'echarts';

export default defineComponent({
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
    },
    unit: {
      type: String,
      default: 'ms'
    }
  },
  setup(props) {
    const chartContainer = ref(null);
    let chartInstance = null;

    const initChart = () => {
      if (!chartContainer.value) return;
      
      // Crear instancia de ECharts
      chartInstance = echarts.init(chartContainer.value);
      
      // Generar etiquetas para el eje X (últimos N minutos)
      const labels = [];
      for (let i = props.data.length - 1; i >= 0; i--) {
        labels.push(`-${i}m`);
      }
      
      // Configuración del gráfico
      const option = {
        grid: {
          top: 10,
          right: 10,
          bottom: 20,
          left: 40,
          containLabel: true
        },
        xAxis: {
          type: 'category',
          data: labels,
          axisLine: {
            lineStyle: {
              color: 'rgba(255, 255, 255, 0.2)'
            }
          },
          axisLabel: {
            color: '#aaa',
            fontSize: 10
          }
        },
        yAxis: {
          type: 'value',
          min: 0,
          max: props.maxValue,
          axisLine: {
            lineStyle: {
              color: 'rgba(255, 255, 255, 0.2)'
            }
          },
          splitLine: {
            lineStyle: {
              color: 'rgba(255, 255, 255, 0.1)'
            }
          },
          axisLabel: {
            color: '#aaa',
            fontSize: 10
          }
        },
        series: [
          {
            data: props.data,
            type: 'line',
            smooth: true,
            symbol: 'circle',
            symbolSize: 6,
            lineStyle: {
              color: '#F08A24',
              width: 3
            },
            itemStyle: {
              color: '#F08A24'
            },
            areaStyle: {
              color: new echarts.graphic.LinearGradient(0, 0, 0, 1, [
                {
                  offset: 0,
                  color: 'rgba(240, 138, 36, 0.5)'
                },
                {
                  offset: 1,
                  color: 'rgba(240, 138, 36, 0.1)'
                }
              ])
            }
          }
        ],
        tooltip: {
          trigger: 'axis',
          backgroundColor: 'rgba(0, 0, 0, 0.7)',
          borderColor: '#F08A24',
          textStyle: {
            color: '#fff'
          },
          formatter: function(params) {
            return `${params[0].value}${props.unit}`;
          }
        },
        animation: true
      };
      
      // Aplicar configuración
      chartInstance.setOption(option);
    };

    // Actualizar el gráfico cuando cambian los datos
    watch(() => props.data, () => {
      if (chartInstance) {
        const labels = [];
        for (let i = props.data.length - 1; i >= 0; i--) {
          labels.push(`-${i}m`);
        }
        
        chartInstance.setOption({
          xAxis: {
            data: labels
          },
          series: [{
            data: props.data
          }]
        });
      }
    }, { deep: true });

    // Manejar el redimensionamiento
    const handleResize = () => {
      if (chartInstance) {
        chartInstance.resize();
      }
    };

    onMounted(() => {
      // Inicializar el gráfico después de que el DOM esté listo
      setTimeout(() => {
        initChart();
        window.addEventListener('resize', handleResize);
      }, 300);
    });

    onUnmounted(() => {
      // Limpiar cuando el componente se desmonta
      if (chartInstance) {
        chartInstance.dispose();
      }
      window.removeEventListener('resize', handleResize);
    });

    return {
      chartContainer
    };
  }
});
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

.echarts-container {
  flex: 1;
  width: 100%;
  min-height: 150px;
}

@media (min-width: 768px) {
  .echarts-container {
    min-height: 200px;
  }
}
</style>
