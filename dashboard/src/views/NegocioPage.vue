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
                <!-- 2. Gráfico de Chart.js - Valoración media de usuarios -->
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
                <!-- 3. Gráfico de ApexCharts - Usuarios activos en creación de mazos -->
                <div class="chart-container">
                  <h3>Usuarios activos en creación de mazos</h3>
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
                <!-- 4. Gráfico de ECharts - Tasa de conversión de compradores -->
                <div class="chart-container">
                  <h3>Tasa de conversión de compradores</h3>
                  <div class="kpi-value">{{ conversionRate }}<span class="unit">%</span></div>
                  <div ref="conversionChart" class="echarts-container"></div>
                </div>
              </div>
            </ion-col>
            <ion-col size="12" size-md="4">
              <div class="dashboard-card">
                <!-- 5. Gráfico en tiempo real - Número de respuestas de feedback -->
                <div class="chart-container">
                  <div class="chart-header">
                    <h3>Respuestas de feedback</h3>
                    <div class="live-badge">
                      <div class="pulse"></div>
                      <span>En vivo</span>
                    </div>
                  </div>
                  <div class="kpi-value">{{ feedbackResponses }}<span class="unit">respuestas</span></div>
                  <div class="chart-wrapper">
                    <canvas ref="feedbackChart"></canvas>
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
    let updateInterval = null;
    
    // Datos para el gráfico de feedback en tiempo real
    const feedbackData = [42, 38, 45, 50, 55, 60, 58, 62, 67, 70, 75, 80];
    const timeLabels = ['12:00', '12:05', '12:10', '12:15', '12:20', '12:25', '12:30', '12:35', '12:40', '12:45', '12:50', '12:55'];
    
    // Función para actualizar el gráfico de feedback en tiempo real
    const updateFeedbackChart = () => {
      if (feedbackChartInstance) {
        // Generar un nuevo valor aleatorio entre -5 y +10 del último valor
        const lastValue = feedbackData[feedbackData.length - 1];
        const change = Math.floor(Math.random() * 16) - 5; // Entre -5 y +10
        const newValue = Math.max(0, lastValue + change);
        feedbackResponses.value = newValue;
        
        // Actualizar datos y etiquetas
        feedbackData.push(newValue);
        feedbackData.shift();
        
        const now = new Date();
        const timeString = `${now.getHours()}:${now.getMinutes().toString().padStart(2, '0')}`;
        timeLabels.push(timeString);
        timeLabels.shift();
        
        // Actualizar el gráfico
        feedbackChartInstance.data.labels = timeLabels;
        feedbackChartInstance.data.datasets[0].data = feedbackData;
        feedbackChartInstance.update();
      }
    };
    
    onMounted(() => {
      // Asegurarse de que los contenedores de gráficos tengan el tamaño correcto
      setTimeout(() => {
        // 1. Inicializar Chart.js para el gráfico de valoración
        if (ratingChart.value) {
          const ctx = ratingChart.value.getContext('2d');
          
          // Datos de valoraciones (1-5 estrellas)
          const ratingDistribution = [15, 30, 120, 450, 1200];
          
          chartJsRatingInstance = new Chart(ctx, {
            type: 'bar',
            data: {
              labels: ['1★', '2★', '3★', '4★', '5★'],
              datasets: [{
                label: 'Valoraciones',
                data: ratingDistribution,
                backgroundColor: [
                  'rgba(255, 99, 132, 0.7)',
                  'rgba(255, 159, 64, 0.7)',
                  'rgba(255, 205, 86, 0.7)',
                  'rgba(75, 192, 192, 0.7)',
                  'rgba(240, 138, 36, 0.7)'
                ],
                borderColor: [
                  'rgb(255, 99, 132)',
                  'rgb(255, 159, 64)',
                  'rgb(255, 205, 86)',
                  'rgb(75, 192, 192)',
                  'rgb(240, 138, 36)'
                ],
                borderWidth: 1
              }]
            },
            options: {
              responsive: true,
              maintainAspectRatio: false,
              plugins: {
                legend: {
                  display: false
                }
              },
              scales: {
                y: {
                  beginAtZero: true,
                  grid: {
                    color: 'rgba(255, 255, 255, 0.1)'
                  },
                  ticks: {
                    color: '#aaa'
                  }
                },
                x: {
                  grid: {
                    display: false
                  },
                  ticks: {
                    color: '#aaa'
                  }
                }
              }
            }
          });
        }
        
        // 2. Inicializar ApexCharts para el gráfico de usuarios activos
        if (activeUsersChart.value) {
          const options = {
            series: [{
              name: 'Usuarios activos',
              data: [2100, 2300, 2500, 2700, 2900, 3100, 3245]
            }],
            chart: {
              type: 'area',
              height: '100%',
              toolbar: {
                show: false
              },
              background: 'transparent',
              foreColor: '#aaa'
            },
            colors: ['#F08A24'],
            stroke: {
              curve: 'smooth',
              width: 3
            },
            fill: {
              type: 'gradient',
              gradient: {
                shadeIntensity: 1,
                opacityFrom: 0.7,
                opacityTo: 0.2,
                stops: [0, 90, 100]
              }
            },
            dataLabels: {
              enabled: false
            },
            xaxis: {
              categories: ['Abr', 'May', 'Jun', 'Jul', 'Ago', 'Sep', 'Oct'],
              labels: {
                style: {
                  colors: '#aaa'
                }
              }
            },
            yaxis: {
              labels: {
                style: {
                  colors: '#aaa'
                }
              }
            },
            grid: {
              borderColor: 'rgba(255, 255, 255, 0.1)'
            },
            tooltip: {
              theme: 'dark'
            }
          };
          
          apexChartInstance = new ApexCharts(activeUsersChart.value, options);
          apexChartInstance.render();
        }
        
        // 3. Inicializar ECharts para el gráfico de tasa de conversión
        if (conversionChart.value) {
          echartsInstance = echarts.init(conversionChart.value);
          
          const option = {
            tooltip: {
              trigger: 'axis',
              backgroundColor: 'rgba(0, 0, 0, 0.7)',
              borderColor: '#F08A24',
              textStyle: {
                color: '#fff'
              }
            },
            grid: {
              top: 10,
              right: 10,
              bottom: 20,
              left: 40,
              containLabel: true
            },
            xAxis: {
              type: 'category',
              data: ['Ene', 'Feb', 'Mar', 'Abr', 'May', 'Jun', 'Jul', 'Ago', 'Sep', 'Oct'],
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
              max: 10,
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
                fontSize: 10,
                formatter: '{value}%'
              }
            },
            series: [
              {
                name: 'Tasa de conversión',
                type: 'line',
                smooth: true,
                symbol: 'circle',
                symbolSize: 8,
                data: [5.2, 5.5, 5.8, 6.2, 6.5, 6.8, 7.2, 7.5, 7.9, 8.3],
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
                },
                markLine: {
                  silent: true,
                  lineStyle: {
                    color: '#F08A24',
                    type: 'dashed'
                  },
                  data: [
                    {
                      yAxis: 7.0,
                      label: {
                        formatter: 'Objetivo: 7%',
                        position: 'start',
                        color: '#F08A24'
                      }
                    }
                  ]
                }
              }
            ],
            animation: true
          };
          
          echartsInstance.setOption(option);
        }
        
        // 4. Inicializar Chart.js para el gráfico de feedback en tiempo real
        if (feedbackChart.value) {
          const ctx = feedbackChart.value.getContext('2d');
          feedbackChartInstance = new Chart(ctx, {
            type: 'line',
            data: {
              labels: timeLabels,
              datasets: [{
                label: 'Respuestas de feedback',
                data: feedbackData,
                borderColor: '#F08A24',
                backgroundColor: 'rgba(240, 138, 36, 0.1)',
                tension: 0.4,
                fill: true
              }]
            },
            options: {
              responsive: true,
              maintainAspectRatio: false,
              plugins: {
                legend: {
                  display: false
                }
              },
              scales: {
                y: {
                  beginAtZero: true,
                  grid: {
                    color: 'rgba(255, 255, 255, 0.1)'
                  },
                  ticks: {
                    color: '#aaa'
                  }
                },
                x: {
                  grid: {
                    color: 'rgba(255, 255, 255, 0.1)'
                  },
                  ticks: {
                    color: '#aaa',
                    maxRotation: 0,
                    autoSkip: true,
                    maxTicksLimit: 6
                  }
                }
              }
            }
          });
        }
        
        // Manejar el redimensionamiento para todos los gráficos
        const handleResize = () => {
          if (echartsInstance) {
            echartsInstance.resize();
          }
          if (apexChartInstance) {
            apexChartInstance.render();
          }
        };
        
        window.addEventListener('resize', handleResize);
        
        // Iniciar la actualización en tiempo real para el gráfico de feedback
        updateInterval = setInterval(updateFeedbackChart, 3000);
      }, 300);
    });
    
    onUnmounted(() => {
      // Limpiar el intervalo cuando el componente se desmonta
      if (updateInterval) {
        clearInterval(updateInterval);
      }
      
      // Destruir las instancias de los gráficos
      if (echartsInstance) {
        echartsInstance.dispose();
      }
      
      if (apexChartInstance) {
        apexChartInstance.destroy();
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
