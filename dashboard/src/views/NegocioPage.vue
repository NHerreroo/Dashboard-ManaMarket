<template>
  <ion-page>
    <ion-header :translucent="true">
      <ion-toolbar class="dark-toolbar">
        <ion-buttons slot="start">
          <ion-menu-button color="light"></ion-menu-button>
        </ion-buttons>
        <ion-title>🚀 Negocio</ion-title>
      </ion-toolbar>
    </ion-header>

    <ion-content class="dark-content">
      <div class="dashboard-container">
        <!-- Grid principal del Dashboard -->
        <ion-grid>
          <!-- Fila 1: KPIs principales -->
          <ion-row>
            <ion-col size="12" size-md="6" size-lg="4">
              <div class="dashboard-card">
                <!-- 1. Gráfico propio (componente) - Número de descargas -->
                <downloads-chart 
                  :data="downloadsData" 
                  :current-value="totalDownloads"
                />
              </div>
            </ion-col>
            <ion-col size="12" size-md="6" size-lg="4">
              <div class="dashboard-card">
                <!-- 2. Gráfico de Chart.js - Valoración media de usuarios (RADAR CHART) -->
                <div class="chart-container">
                  <h3>Valoración media de usuarios</h3>
                  <div class="kpi-value">{{ averageRating }}<span class="unit">/5</span></div>
                  <div class="chart-wrapper">
                    <canvas ref="ratingChart"></canvas>
                  </div>
                </div>
              </div>
            </ion-col>
            <ion-col size="12" size-md="6" size-lg="4">
              <div class="dashboard-card">
                <!-- 3. Gráfico de ApexCharts - Usuarios activos en creación de mazos (TIEMPO REAL) -->
                <div class="chart-container">
                  <div class="chart-header">
                    <h3>Usuarios activos en creación de mazos</h3>
                    <div class="live-badge">
                      <div class="pulse"></div>
                      <span>En vivo</span>
                    </div>
                  </div>
                  <div class="kpi-value">{{ activeUsers }}<span class="unit">usuarios</span></div>
                  <div class="chart-wrapper">
                    <div ref="activeUsersChart"></div>
                  </div>
                </div>
              </div>
            </ion-col>
          </ion-row>

          <!-- Fila 2: Gráficos adicionales -->
          <ion-row>
            <ion-col size="12" size-md="8">
              <div class="dashboard-card">
                <!-- 4. Gráfico de ECharts - Tasa de conversión de compradores (GAUGE CHART) -->
                <div class="chart-container">
                  <h3>Tasa de conversión de compradores</h3>
                  <div class="kpi-value">{{ conversionRate }}<span class="unit">%</span></div>
                  <div ref="conversionChart" class="echarts-container"></div>
                </div>
              </div>
            </ion-col>
            <ion-col size="12" size-md="4">
              <div class="dashboard-card">
                <!-- 5. Gráfico de feedback (HEATMAP) -->
                <div class="chart-container">
                  <h3>Respuestas de feedback</h3>
                  <div class="kpi-value">{{ feedbackResponses }}<span class="unit">respuestas</span></div>
                  <div class="chart-wrapper">
                    <div ref="feedbackChart"></div>
                  </div>
                </div>
              </div>
            </ion-col>
          </ion-row>
        </ion-grid>
      </div>
    </ion-content>
  </ion-page>
</template>

<script>
import { IonPage, IonHeader, IonToolbar, IonTitle, IonContent, IonGrid, IonRow, IonCol, IonButtons, IonMenuButton } from '@ionic/vue';
import { defineComponent, ref, onMounted, onUnmounted } from 'vue';
import Chart from 'chart.js/auto';
import * as echarts from 'echarts';
import ApexCharts from 'apexcharts';
import DownloadsChart from '../components/DownloadsChart.vue';

export default defineComponent({
  name: 'NegocioPage',
  components: {
    IonPage, IonHeader, IonToolbar, IonTitle, IonContent, 
    IonGrid, IonRow, IonCol, IonButtons, IonMenuButton,
    DownloadsChart
  },
  setup() {
    // Referencias para los gráficos
    const ratingChart = ref(null);
    const activeUsersChart = ref(null);
    const conversionChart = ref(null);
    const feedbackChart = ref(null);
    
    // Datos para los KPIs
    const totalDownloads = ref(12458);
    const averageRating = ref(4.7);
    const activeUsers = ref(3245);
    const conversionRate = ref(8.3);
    const feedbackResponses = ref(427);
    
    // Datos para los gráficos
    const downloadsData = ref([
      { month: 'Ene', value: 850 },
      { month: 'Feb', value: 740 },
      { month: 'Mar', value: 900 },
      { month: 'Abr', value: 1200 },
      { month: 'May', value: 1350 },
      { month: 'Jun', value: 1500 },
      { month: 'Jul', value: 1650 },
      { month: 'Ago', value: 1800 },
      { month: 'Sep', value: 1950 },
      { month: 'Oct', value: 2100 },
      { month: 'Nov', value: 2250 },
      { month: 'Dic', value: 2400 }
    ]);
    
    // Variables para los gráficos
    let chartJsRatingInstance = null;
    let apexChartInstance = null;
    let echartsInstance = null;
    let feedbackChartInstance = null;
    let updateActiveUsersInterval = null;
    
    // Datos para el gráfico de usuarios activos en tiempo real
    const activeUsersData = [3150, 3220, 3180, 3250, 3190, 3245];
    const timeLabels = [];

    // Inicializar etiquetas de tiempo
    const initTimeLabels = () => {
      timeLabels.length = 0; // Limpiar array
      const now = new Date();
      // Crear etiquetas para los últimos 30 minutos en intervalos de 5 minutos
      for (let i = 5; i >= 0; i--) {
        const time = new Date(now.getTime() - i * 5 * 60000);
        const timeString = `${time.getHours()}:${time.getMinutes().toString().padStart(2, '0')}`;
        timeLabels.push(timeString);
      }
    };
    
    // Función para actualizar el gráfico de usuarios activos en tiempo real
    const updateActiveUsersChart = () => {
      if (apexChartInstance) {
        // Obtener el último valor
        const lastValue = activeUsersData[activeUsersData.length - 1];
        
        // Calcular tendencia basada en la hora del día
        const now = new Date();
        const hour = now.getHours();
        
        // Simular patrones de uso más realistas basados en la hora
        let baseTrend = 0;
        
        // Más usuarios activos durante la tarde/noche (17:00-23:00)
        if (hour >= 17 && hour < 23) {
          baseTrend = 15;
        } 
        // Menos usuarios por la mañana temprano (0:00-8:00)
        else if (hour < 8) {
          baseTrend = -10;
        } 
        // Actividad moderada durante el día (8:00-17:00)
        else {
          baseTrend = 5;
        }
        
        // Añadir variación aleatoria significativa para crear picos y valles
        const randomFactor = Math.random();
        let variation;
        
        if (randomFactor < 0.2) {
          // 20% de probabilidad de una caída significativa
          variation = Math.floor(Math.random() * -80) - 30;
        } else if (randomFactor < 0.4) {
          // 20% de probabilidad de un pico significativo
          variation = Math.floor(Math.random() * 80) + 30;
        } else {
          // 60% de probabilidad de fluctuaciones menores
          variation = Math.floor(Math.random() * 40) - 20;
        }
        
        // Calcular el nuevo valor con la tendencia base y la variación
        const newValue = Math.max(3000, Math.min(3500, lastValue + baseTrend + variation));
        activeUsers.value = newValue;
        
        // Actualizar datos manteniendo un número fijo de puntos
        const MAX_POINTS = 12;
        
        // Añadir nuevo valor
        activeUsersData.push(newValue);
        
        // Mantener solo los últimos MAX_POINTS puntos
        if (activeUsersData.length > MAX_POINTS) {
          activeUsersData.shift();
        }
        
        // Actualizar etiquetas de tiempo
        const timeString = `${now.getHours()}:${now.getMinutes().toString().padStart(2, '0')}`;
        timeLabels.push(timeString);
        if (timeLabels.length > MAX_POINTS) {
          timeLabels.shift();
        }
        
        // Actualizar el gráfico con los nuevos datos
        apexChartInstance.updateSeries([{
          data: activeUsersData
        }]);
        
        apexChartInstance.updateOptions({
          xaxis: {
            categories: timeLabels
          }
        });
      }
    };
    
    onMounted(() => {
      // Asegurarse de que los contenedores de gráficos tengan el tamaño correcto
      setTimeout(() => {
        // 1. Inicializar Chart.js para el gráfico de valoración (RADAR CHART)
        if (ratingChart.value) {
          const ctx = ratingChart.value.getContext('2d');
          
          chartJsRatingInstance = new Chart(ctx, {
            type: 'radar',
            data: {
              labels: ['Usabilidad', 'Diseño', 'Funcionalidad', 'Velocidad', 'Estabilidad'],
              datasets: [{
                label: 'Valoración',
                data: [4.8, 4.9, 4.5, 4.6, 4.7],
                backgroundColor: 'rgba(240, 138, 36, 0.3)',
                borderColor: '#F08A24',
                borderWidth: 2,
                pointBackgroundColor: '#F08A24',
                pointBorderColor: '#fff',
                pointHoverBackgroundColor: '#fff',
                pointHoverBorderColor: '#F08A24'
              }]
            },
            options: {
              responsive: true,
              maintainAspectRatio: false,
              scales: {
                r: {
                  angleLines: {
                    color: 'rgba(255, 255, 255, 0.1)'
                  },
                  grid: {
                    color: 'rgba(255, 255, 255, 0.1)'
                  },
                  pointLabels: {
                    color: '#aaa',
                    font: {
                      size: 10
                    }
                  },
                  ticks: {
                    color: '#aaa',
                    backdropColor: 'transparent',
                    stepSize: 1,
                    max: 5,
                    min: 0
                  }
                }
              },
              plugins: {
                legend: {
                  display: false
                },
                tooltip: {
                  backgroundColor: 'rgba(0, 0, 0, 0.7)',
                  titleColor: '#fff',
                  bodyColor: '#fff',
                  borderColor: '#F08A24',
                  borderWidth: 1
                }
              }
            }
          });
        }
        
        // Inicializar etiquetas de tiempo
        initTimeLabels();

        // 2. Inicializar ApexCharts para el gráfico de usuarios activos (CANDLESTICK CHART)
        if (activeUsersChart.value) {
          // Generar datos para candlestick
          const candleData = [];
          const baseValue = 3200;
          
          for (let i = 0; i < 12; i++) {
            const open = baseValue + Math.floor(Math.random() * 200) - 100;
            const close = open + Math.floor(Math.random() * 100) - 50;
            const low = Math.min(open, close) - Math.floor(Math.random() * 50);
            const high = Math.max(open, close) + Math.floor(Math.random() * 50);
            
            candleData.push({
              x: timeLabels[i] || `${i}:00`,
              y: [open, high, low, close]
            });
          }
          
          const options = {
            series: [{
              name: 'Usuarios activos',
              data: candleData
            }],
            chart: {
              type: 'candlestick',
              height: '100%',
              toolbar: {
                show: false
              },
              background: 'transparent',
              foreColor: '#aaa',
              animations: {
                enabled: true,
                easing: 'easeinout',
                speed: 800
              }
            },
            plotOptions: {
              candlestick: {
                colors: {
                  upward: '#F08A24',
                  downward: '#FF4560'
                },
                wick: {
                  useFillColor: true
                }
              }
            },
            xaxis: {
              type: 'category',
              labels: {
                style: {
                  colors: '#aaa'
                },
                rotate: -45,
                rotateAlways: false
              }
            },
            yaxis: {
              labels: {
                style: {
                  colors: '#aaa'
                },
                formatter: function(val) {
                  return val.toFixed(0);
                }
              },
              min: 3000,
              max: 3500,
              tickAmount: 5
            },
            grid: {
              borderColor: 'rgba(255, 255, 255, 0.1)'
            },
            tooltip: {
              theme: 'dark',
              custom: function({ seriesIndex, dataPointIndex, w }) {
                const data = w.globals.initialSeries[seriesIndex].data[dataPointIndex];
                return `
                  <div class="apexcharts-tooltip-candlestick" style="padding: 8px; background: rgba(0,0,0,0.8); color: #fff; border: 1px solid #F08A24;">
                    <div>Hora: ${data.x}</div>
                    <div>Apertura: ${data.y[0]}</div>
                    <div>Máximo: ${data.y[1]}</div>
                    <div>Mínimo: ${data.y[2]}</div>
                    <div>Cierre: ${data.y[3]}</div>
                  </div>
                `;
              }
            }
          };
          
          apexChartInstance = new ApexCharts(activeUsersChart.value, options);
          apexChartInstance.render();
          
          // Actualizar el valor actual para mostrar
          activeUsers.value = candleData[candleData.length - 1].y[3];
        }
        
        // 3. Inicializar ECharts para el gráfico de tasa de conversión (GAUGE CHART)
        if (conversionChart.value) {
          echartsInstance = echarts.init(conversionChart.value);
          
          const option = {
            series: [
              {
                type: 'gauge',
                startAngle: 180,
                endAngle: 0,
                center: ['50%', '75%'],
                radius: '90%',
                min: 0,
                max: 15,
                splitNumber: 5,
                axisLine: {
                  lineStyle: {
                    width: 6,
                    color: [
                      [0.3, '#FF4560'],
                      [0.7, '#FFAB00'],
                      [1, '#F08A24']
                    ]
                  }
                },
                pointer: {
                  icon: 'path://M12.8,0.7l12,40.1H0.7L12.8,0.7z',
                  length: '12%',
                  width: 6,
                  offsetCenter: [0, '-60%'],
                  itemStyle: {
                    color: '#F08A24'
                  }
                },
                axisTick: {
                  length: 12,
                  lineStyle: {
                    color: 'auto',
                    width: 1
                  }
                },
                splitLine: {
                  length: 20,
                  lineStyle: {
                    color: 'auto',
                    width: 2
                  }
                },
                axisLabel: {
                  color: '#aaa',
                  fontSize: 10,
                  distance: -60,
                  formatter: function(value) {
                    return value + '%';
                  }
                },
                title: {
                  offsetCenter: [0, '-20%'],
                  fontSize: 12,
                  color: '#aaa'
                },
                detail: {
                  fontSize: 30,
                  offsetCenter: [0, '0%'],
                  valueAnimation: true,
                  formatter: function(value) {
                    return value + '%';
                  },
                  color: '#F08A24'
                },
                data: [
                  {
                    value: 8.3,
                    name: 'Conversión'
                  }
                ]
              }
            ],
            animation: true
          };
          
          echartsInstance.setOption(option);
        }
        
        // 4. Inicializar ApexCharts para el gráfico de feedback (HEATMAP)
        if (feedbackChart.value) {
          // Generar datos para el heatmap
          const generateHeatmapData = () => {
            const categories = ['Escaneo', 'Interfaz', 'Búsqueda', 'Precios', 'Mazos'];
            const series = [];
            
            for (let i = 0; i < 5; i++) {
              const data = [];
              for (let j = 0; j < 5; j++) {
                // Más feedback positivo que negativo
                const value = Math.floor(Math.random() * 30) + (5 - j) * 10;
                data.push(value);
              }
              series.push({
                name: categories[i],
                data: data
              });
            }
            
            return series;
          };
          
          const options = {
            series: generateHeatmapData(),
            chart: {
              height: '100%',
              type: 'heatmap',
              toolbar: {
                show: false
              },
              background: 'transparent'
            },
            dataLabels: {
              enabled: false
            },
            colors: ['#F08A24'],
            xaxis: {
              categories: ['Excelente', 'Bueno', 'Normal', 'Regular', 'Malo'],
              labels: {
                style: {
                  colors: '#aaa',
                  fontSize: '10px'
                },
                rotate: 0
              }
            },
            yaxis: {
              labels: {
                style: {
                  colors: '#aaa',
                  fontSize: '10px'
                }
              }
            },
            plotOptions: {
              heatmap: {
                colorScale: {
                  ranges: [
                    {
                      from: 0,
                      to: 20,
                      color: 'rgba(240, 138, 36, 0.2)',
                      name: 'bajo'
                    },
                    {
                      from: 21,
                      to: 40,
                      color: 'rgba(240, 138, 36, 0.4)',
                      name: 'medio'
                    },
                    {
                      from: 41,
                      to: 60,
                      color: 'rgba(240, 138, 36, 0.6)',
                      name: 'alto'
                    },
                    {
                      from: 61,
                      to: 100,
                      color: 'rgba(240, 138, 36, 0.8)',
                      name: 'muy alto'
                    }
                  ]
                }
              }
            },
            tooltip: {
              theme: 'dark',
              y: {
                formatter: function(val) {
                  return val + ' respuestas';
                }
              }
            }
          };
          
          feedbackChartInstance = new ApexCharts(feedbackChart.value, options);
          feedbackChartInstance.render();
        }
        
        // Manejar el redimensionamiento para todos los gráficos
        const handleResize = () => {
          if (echartsInstance) {
            echartsInstance.resize();
          }
          if (apexChartInstance) {
            apexChartInstance.render();
          }
          if (feedbackChartInstance) {
            feedbackChartInstance.render();
          }
        };
        
        window.addEventListener('resize', handleResize);
        
        // Iniciar la actualización en tiempo real para el gráfico de usuarios activos
        updateActiveUsersInterval = setInterval(updateActiveUsersChart, 2000);
      }, 300);
    });
    
    onUnmounted(() => {
      // Limpiar el intervalo cuando el componente se desmonta
      if (updateActiveUsersInterval) {
        clearInterval(updateActiveUsersInterval);
      }
      
      // Destruir las instancias de los gráficos
      if (echartsInstance) {
        echartsInstance.dispose();
      }
      
      if (apexChartInstance) {
        apexChartInstance.destroy();
      }
      
      if (feedbackChartInstance) {
        feedbackChartInstance.destroy();
      }
      
      // Eliminar el event listener
      window.removeEventListener('resize', () => {});
    });
    
    return {
      totalDownloads,
      averageRating,
      activeUsers,
      conversionRate,
      feedbackResponses,
      downloadsData,
      ratingChart,
      activeUsersChart,
      conversionChart,
      feedbackChart
    };
  }
});
</script>

<style scoped>
/* Estilos para el tema oscuro con naranja */
.dark-toolbar {
  --background: #000000;
  --color: #FFFFFF;
}

.dark-content {
  --background: #000000;
  --color: #FFFFFF;
}

.dashboard-container {
  max-width: 100%;
  overflow-x: hidden;
  padding: 16px;
}

.dashboard-card {
  background: #1E1E1E;
  border-radius: 8px;
  padding: 16px;
  height: 300px;
  margin-bottom: 16px;
  display: flex;
  flex-direction: column;
  overflow: hidden;
}

.chart-container {
  width: 100%;
  height: 100%;
  display: flex;
  flex-direction: column;
  overflow: hidden;
}

.chart-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 10px;
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

/* Indicador en vivo */
.live-badge {
  display: flex;
  align-items: center;
  gap: 5px;
  background-color: rgba(240, 138, 36, 0.2);
  border-radius: 12px;
  padding: 4px 8px;
  font-size: 12px;
  color: #F08A24;
}

.pulse {
  width: 8px;
  height: 8px;
  border-radius: 50%;
  background-color: #F08A24;
  box-shadow: 0 0 0 rgba(240, 138, 36, 0.4);
  animation: pulse 2s infinite;
}

@keyframes pulse {
  0% {
    box-shadow: 0 0 0 0 rgba(240, 138, 36, 0.4);
  }
  70% {
    box-shadow: 0 0 0 10px rgba(240, 138, 36, 0);
  }
  100% {
    box-shadow: 0 0 0 0 rgba(240, 138, 36, 0);
  }
}

/* Contenedor para los gráficos */
.chart-wrapper {
  flex: 1;
  width: 100%;
  position: relative;
  min-height: 150px;
}

.echarts-container {
  flex: 1;
  width: 100%;
  min-height: 150px;
}

/* Ajustes para pantallas más grandes */
@media (min-width: 768px) {
  .dashboard-card {
    height: 350px;
  }
}
</style>