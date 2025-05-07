<template>
  <ion-page>
    <ion-header :translucent="true">
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
        <ion-grid>
          <!-- Fila 1: KPIs principales -->
          <ion-row>
            <ion-col size="12" size-md="6" size-lg="4">
              <div class="dashboard-card">
                <!-- 1. Tiempo medio de respuesta -->
                <response-time-chart 
                  title="Tiempo medio de respuesta"
                  :data="responseTimeData" 
                  :current-value="245"
                  :maxValue="300"
                  unit="ms"
                />
              </div>
            </ion-col>
            <ion-col size="12" size-md="6" size-lg="4">
              <div class="dashboard-card">
                <!-- 2. Disponibilidad del sistema -->
                <div class="chart-container">
                  <div class="chart-header">
                    <h3>Disponibilidad del sistema</h3>
                    <div class="live-badge">
                      <div class="pulse"></div>
                      <span>En vivo</span>
                    </div>
                  </div>
                  <div class="kpi-value">{{ systemAvailability }}<span class="unit">%</span></div>
                  <div class="chart-wrapper">
                    <canvas ref="availabilityChart"></canvas>
                  </div>
                </div>
              </div>
            </ion-col>
            <ion-col size="12" size-md="6" size-lg="4">
              <div class="dashboard-card">
                <!-- 3. Errores de escaneo -->
                <div class="chart-container">
                  <h3>Porcentaje de errores de escaneo</h3>
                  <div class="kpi-value">{{ scanErrorRate }}<span class="unit">%</span></div>
                  <div class="chart-wrapper">
                    <div ref="scanErrorChart"></div>
                  </div>
                </div>
              </div>
            </ion-col>
          </ion-row>

          <!-- Fila 2: Gráficos adicionales -->
          <ion-row>
            <ion-col size="12" size-md="8">
              <div class="dashboard-card">
                <!-- 4. Tiempo de carga de noticias (usando ResponseTimeChart) -->
                <response-time-chart 
                  title="Tiempo de carga de noticias"
                  :data="newsLoadingTimeData" 
                  :current-value="loadingTime"
                  :maxValue="3"
                  unit="s"
                />
              </div>
            </ion-col>
            <ion-col size="12" size-md="4">
              <div class="dashboard-card">
                <!-- 5. Copias de seguridad completadas (CIRCULAR) -->
                <div class="chart-container">
                  <h3>Copias de seguridad completadas</h3>
                  <div class="kpi-value">{{ backupRate }}<span class="unit">%</span></div>
                  <div class="chart-wrapper">
                    <div ref="backupChart" class="donut-chart-container"></div>
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
    const backupChart = ref(null);
    
    // Datos para los KPIs
    const systemAvailability = ref(99.8);
    const scanErrorRate = ref(2.3);
    const loadingTime = ref(1.2);
    const backupRate = ref(96);
    
    // Datos para los gráficos
    const responseTimeData = ref([230, 242, 255, 260, 248, 245, 238, 242, 250, 245]);
    const newsLoadingTimeData = ref([
      1.8, 1.7, 1.9, 1.6, 1.5, 1.4, 1.3, 1.4, 1.5, 1.3
    ]);
    
    // Variables para los gráficos
    let chartJsInstance = null;
    let apexChartInstance = null;
    let donutChartInstance = null;
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
        
        // 4. Inicializar ApexCharts para el gráfico circular de copias de seguridad
        if (backupChart.value) {
          const options = {
            series: [96], // Porcentaje de copias de seguridad completadas
            chart: {
              height: '100%',
              type: 'radialBar',
              toolbar: {
                show: false
              },
              background: 'transparent'
            },
            plotOptions: {
              radialBar: {
                startAngle: -135,
                endAngle: 135,
                hollow: {
                  margin: 0,
                  size: '70%',
                  background: 'transparent',
                },
                track: {
                  background: 'rgba(255, 255, 255, 0.1)',
                  strokeWidth: '97%',
                  margin: 5,
                  dropShadow: {
                    enabled: false
                  }
                },
                dataLabels: {
                  name: {
                    show: false
                  },
                  value: {
                    offsetY: 10,
                    color: '#F08A24',
                    fontSize: '26px',
                    fontWeight: 600,
                    formatter: function (val) {
                      return val + '%';
                    }
                  }
                }
              }
            },
            fill: {
              type: 'gradient',
              gradient: {
                shade: 'dark',
                type: 'horizontal',
                shadeIntensity: 0.5,
                gradientToColors: ['#F08A24'],
                inverseColors: true,
                opacityFrom: 1,
                opacityTo: 1,
                stops: [0, 100]
              }
            },
            stroke: {
              dashArray: 4
            },
            colors: ['#F08A24'],
            labels: ['Completadas']
          };
          
          donutChartInstance = new ApexCharts(backupChart.value, options);
          donutChartInstance.render();
        }
        
        // Manejar el redimensionamiento para todos los gráficos
        const handleResize = () => {
          if (apexChartInstance) {
            apexChartInstance.render();
          }
          if (donutChartInstance) {
            donutChartInstance.render();
          }
        };
        
        window.addEventListener('resize', handleResize);
        
        // Iniciar la actualización en tiempo real para el gráfico de disponibilidad
        updateInterval = setInterval(updateAvailabilityChart, 3000);
      }, 300);
    });
    
    onUnmounted(() => {
      // Limpiar el intervalo cuando el componente se desmonta
      if (updateInterval) {
        clearInterval(updateInterval);
      }
      
      // Destruir las instancias de los gráficos
      if (apexChartInstance) {
        apexChartInstance.destroy();
      }
      
      if (donutChartInstance) {
        donutChartInstance.destroy();
      }
      
      // Eliminar el event listener
      window.removeEventListener('resize', () => {});
    });
    
    return {
      systemAvailability,
      scanErrorRate,
      loadingTime,
      backupRate,
      responseTimeData,
      newsLoadingTimeData,
      availabilityChart,
      scanErrorChart,
      backupChart
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

/* Contenedor para el gráfico circular */
.donut-chart-container {
  width: 100%;
  height: 100%;
}

/* Ajustes para pantallas más grandes */
@media (min-width: 768px) {
  .dashboard-card {
    height: 350px;
  }
}
</style>
