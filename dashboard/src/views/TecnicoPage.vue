<template>
  <ion-page>
    <ion-header>
      <ion-toolbar class="dark-toolbar">
        <ion-buttons slot="start">
          <ion-menu-button color="light"></ion-menu-button>
        </ion-buttons>
        <ion-title>⚙️ Técnico</ion-title>
      </ion-toolbar>
    </ion-header>

    <ion-content class="dark-content">
      <div class="dashboard-container">
        <!-- Grid principal del Dashboard -->
        <ion-grid fixed>
          <!-- Fila 1: KPIs principales -->
          <ion-row>
            <ion-col size="12" size-md="4">
              <div class="dashboard-card">
                <!-- 1. Gráfico personalizado (componente propio) -->
                <response-time-chart 
                  :data="responseTimeData" 
                  :current-value="responseTime"
                />
              </div>
            </ion-col>
            <ion-col size="12" size-md="4">
              <div class="dashboard-card">
                <div class="chart-container">
                  <div class="chart-header">
                    <h3>Disponibilidad del sistema</h3>
                    <div class="live-badge">
                      <div class="pulse"></div>
                      <span>En vivo</span>
                    </div>
                  </div>
                  <div class="kpi-value">{{ systemAvailability }}<span class="unit">%</span></div>
                  <!-- 2. Gráfico de Chart.js en tiempo real -->
                  <div class="chart-wrapper">
                    <canvas ref="availabilityChart"></canvas>
                  </div>
                </div>
              </div>
            </ion-col>
            <ion-col size="12" size-md="4">
              <div class="dashboard-card">
                <div class="chart-container">
                  <h3>Errores de escaneo</h3>
                  <div class="kpi-value">{{ scanErrorRate }}<span class="unit">%</span></div>
                  <!-- 3. Gráfico de ApexChart -->
                  <div ref="scanErrorChart" class="chart-wrapper"></div>
                </div>
              </div>
            </ion-col>
          </ion-row>

          <!-- Fila 2: Gráficos adicionales -->
          <ion-row>
            <ion-col size="12" size-md="8">
              <div class="dashboard-card">
                <div class="chart-container">
                  <h3>Tiempo de carga de noticias</h3>
                  <div class="kpi-value">{{ loadingTime }}<span class="unit">s</span></div>
                  <!-- 4. Gráfico de EChart -->
                  <div ref="loadingTimeChart" class="chart-wrapper"></div>
                </div>
              </div>
            </ion-col>
            <ion-col size="12" size-md="4">
              <div class="dashboard-card">
                <div class="chart-container">
                  <h3>Copias de seguridad completadas</h3>
                  <div class="kpi-value">{{ backupRate }}<span class="unit">%</span></div>
                  <!-- 5. Gráfico simple de barras -->
                  <div class="backup-bars">
                    <div 
                      v-for="(value, i) in backupData" 
                      :key="i" 
                      class="backup-bar"
                      :style="{ height: `${value}%` }"
                    ></div>
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
import ResponseTimeChart from '../components/ResponseTimeChart.vue';

export default defineComponent({
  name: 'TecnicoPage',
  components: {
    IonPage, IonHeader, IonToolbar, IonTitle, IonContent, 
    IonGrid, IonRow, IonCol, IonButtons, IonMenuButton,
    ResponseTimeChart
  },
  setup() {
    // Referencias para los gráficos
    const availabilityChart = ref(null);
    const scanErrorChart = ref(null);
    const loadingTimeChart = ref(null);
    
    // Datos para los KPIs
    const responseTime = ref(245);
    const systemAvailability = ref(99.8);
    const scanErrorRate = ref(2.3);
    const loadingTime = ref(1.2);
    const backupRate = ref(96);
    
    // Datos para los gráficos
    const responseTimeData = ref([230, 242, 255, 260, 248, 245, 238, 242, 250, 245]);
    const backupData = ref([85, 90, 95, 100, 92, 96, 98, 94, 90, 96]);
    
    // Variables para los gráficos
    let chartJsInstance = null;
    let apexChartInstance = null;
    let echartsInstance = null;
    let updateInterval = null;
    
    // Datos para el gráfico de disponibilidad en tiempo real
    const availabilityData = [99.7, 99.8, 99.9, 99.7, 99.6, 99.8, 99.9, 99.8, 99.7, 99.8, 99.9, 99.8];
    const timeLabels = ['12:00', '12:05', '12:10', '12:15', '12:20', '12:25', '12:30', '12:35', '12:40', '12:45', '12:50', '12:55'];
    
    // Función para actualizar el gráfico de disponibilidad en tiempo real
    const updateAvailabilityChart = () => {
      if (chartJsInstance) {
        // Generar un nuevo valor aleatorio entre 99.5 y 100
        const newValue = (99.5 + Math.random() * 0.5).toFixed(1);
        systemAvailability.value = parseFloat(newValue);
        
        // Actualizar datos y etiquetas
        availabilityData.push(newValue);
        availabilityData.shift();
        
        const now = new Date();
        const timeString = `${now.getHours()}:${now.getMinutes().toString().padStart(2, '0')}`;
        timeLabels.push(timeString);
        timeLabels.shift();
        
        // Actualizar el gráfico
        chartJsInstance.data.labels = timeLabels;
        chartJsInstance.data.datasets[0].data = availabilityData;
        chartJsInstance.update();
      }
    };
    
    onMounted(() => {
      // Asegurarse de que los contenedores de gráficos tengan el tamaño correcto
      setTimeout(() => {
        // 1. Inicializar Chart.js para el gráfico de disponibilidad en tiempo real
        if (availabilityChart.value) {
          const ctx = availabilityChart.value.getContext('2d');
          chartJsInstance = new Chart(ctx, {
            type: 'line',
            data: {
              labels: timeLabels,
              datasets: [{
                label: 'Disponibilidad (%)',
                data: availabilityData,
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
                  min: 99,
                  max: 100,
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
                    color: '#aaa'
                  }
                }
              }
            }
          });
        }
        
        // 2. Inicializar ApexCharts para el gráfico de errores de escaneo
        if (scanErrorChart.value) {
          const options = {
            series: [{
              name: 'Errores de escaneo',
              data: [3.8, 3.2, 2.9, 2.7, 2.5, 2.3]
            }],
            chart: {
              type: 'bar',
              height: '100%',
              toolbar: {
                show: false
              },
              background: 'transparent',
              foreColor: '#aaa'
            },
            colors: ['#F08A24'],
            plotOptions: {
              bar: {
                borderRadius: 4,
                columnWidth: '50%',
              }
            },
            dataLabels: {
              enabled: false
            },
            xaxis: {
              categories: ['May', 'Jun', 'Jul', 'Ago', 'Sep', 'Oct'],
              labels: {
                style: {
                  colors: '#aaa'
                }
              }
            },
            yaxis: {
              min: 0,
              max: 5,
              labels: {
                style: {
                  colors: '#aaa'
                }
              }
            },
            grid: {
              borderColor: 'rgba(255, 255, 255, 0.1)'
            }
          };
          
          apexChartInstance = new ApexCharts(scanErrorChart.value, options);
          apexChartInstance.render();
        }
        
        // 3. Inicializar ECharts para el gráfico de tiempo de carga
        if (loadingTimeChart.value) {
          echartsInstance = echarts.init(loadingTimeChart.value);
          const option = {
            backgroundColor: 'transparent',
            tooltip: {
              trigger: 'axis'
            },
            grid: {
              left: '3%',
              right: '4%',
              bottom: '3%',
              containLabel: true
            },
            xAxis: {
              type: 'category',
              boundaryGap: false,
              data: Array.from({ length: 30 }, (_, i) => i + 1),
              axisLine: {
                lineStyle: {
                  color: 'rgba(255, 255, 255, 0.3)'
                }
              },
              axisLabel: {
                color: '#aaa'
              }
            },
            yAxis: {
              type: 'value',
              min: 0,
              max: 3,
              axisLine: {
                lineStyle: {
                  color: 'rgba(255, 255, 255, 0.3)'
                }
              },
              splitLine: {
                lineStyle: {
                  color: 'rgba(255, 255, 255, 0.1)'
                }
              },
              axisLabel: {
                color: '#aaa'
              }
            },
            series: [
              {
                name: 'Tiempo de carga (s)',
                type: 'line',
                smooth: true,
                lineStyle: {
                  width: 3,
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
                data: [
                  1.8, 1.7, 1.9, 1.6, 1.5, 1.4, 1.3, 1.4, 1.5, 1.3,
                  1.2, 1.3, 1.2, 1.1, 1.2, 1.3, 1.2, 1.1, 1.0, 1.2,
                  1.1, 1.0, 1.1, 1.2, 1.1, 1.2, 1.3, 1.2, 1.1, 1.2
                ]
              }
            ]
          };
          echartsInstance.setOption(option);
        }
      }, 100);
      
      // Manejar el redimensionamiento para todos los gráficos
      const handleResize = () => {
        if (echartsInstance) {
          echartsInstance.resize();
        }
      };
      
      window.addEventListener('resize', handleResize);
      
      // Iniciar la actualización en tiempo real para el gráfico de disponibilidad
      updateInterval = setInterval(updateAvailabilityChart, 3000);
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
      responseTime,
      systemAvailability,
      scanErrorRate,
      loadingTime,
      backupRate,
      responseTimeData,
      backupData,
      availabilityChart,
      scanErrorChart,
      loadingTimeChart
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
}

.dashboard-card {
  background: #1E1E1E;
  border-radius: 8px;
  padding: 16px;
  height: 250px; /* Altura fija para evitar expansión */
  margin-bottom: 16px;
  display: flex;
  flex-direction: column;
  overflow: hidden; /* Evita que el contenido se desborde */
}

.chart-container {
  width: 100%;
  height: 100%;
  display: flex;
  flex-direction: column;
  overflow: hidden; /* Evita que el contenido se desborde */
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

/* Contenedor para los gráficos con altura fija */
.chart-wrapper {
  flex: 1;
  height: 150px; /* Altura fija para los gráficos */
  max-height: 150px;
  width: 100%;
  position: relative;
  overflow: hidden;
}

/* Gráfico de barras para copias de seguridad */
.backup-bars {
  flex: 1;
  display: flex;
  align-items: flex-end;
  gap: 6px;
  height: 150px;
  max-height: 150px;
}

.backup-bar {
  flex: 1;
  background-color: #F08A24;
  border-radius: 4px 4px 0 0;
  min-height: 4px;
  transition: height 0.3s ease;
}

/* Ajustes para pantallas más grandes */
@media (min-width: 768px) {
  .dashboard-card {
    height: 300px; /* Un poco más alto en pantallas grandes */
  }
  
  .chart-wrapper {
    height: 200px;
    max-height: 200px;
  }
  
  .backup-bars {
    height: 200px;
    max-height: 200px;
  }
}
</style>