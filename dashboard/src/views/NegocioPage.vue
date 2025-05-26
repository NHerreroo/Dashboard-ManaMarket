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
                <!-- 2. Gráfico de Chart.js - Evaluación por aspectos (ESTRELLA) -->
                <div class="chart-container">
                  <h3>Evaluación de la App</h3>
                  <div class="kpi-value">{{ averageRating }}<span class="unit">/5</span></div>
                  <div class="chart-wrapper">
                    <canvas ref="ratingChart"></canvas>
                  </div>
                  <div class="rating-legend">
                    <div class="legend-item">
                      <span class="legend-color" style="background-color: rgba(240, 138, 36, 0.8)"></span>
                      <span>Puntuación actual</span>
                    </div>
                  </div>
                </div>
              </div>
            </ion-col>
            <ion-col size="12" size-md="6" size-lg="4">
              <div class="dashboard-card">
                <!-- 3. Gráfico de ApexCharts - Usuarios activos en creación de mazos (AHORA EN TIEMPO REAL) -->
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
                <!-- 4. Gráfico de ECharts - Tasa de conversión de compradores (BARRAS HORIZONTALES) -->
                <div class="chart-container">
                  <div class="chart-header">
                    <h3>Evolución de tasa de conversión</h3>
                    <div class="conversion-status">
                      <span class="status-indicator success"></span>
                      <span class="status-text">Objetivo superado</span>
                    </div>
                  </div>
                  <div class="kpi-value">{{ conversionRate }}<span class="unit">%</span></div>
                  <div ref="conversionChart" class="echarts-container"></div>
                </div>
              </div>
            </ion-col>
            <ion-col size="12" size-md="4">
              <div class="dashboard-card">
                <!-- 5. Gráfico de feedback -->
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
      timeLabels.length = 0;
      const now = new Date();
      for (let i = 5; i >= 0; i--) {
        const time = new Date(now.getTime() - i * 5 * 60000);
        const timeString = `${time.getHours()}:${time.getMinutes().toString().padStart(2, '0')}`;
        timeLabels.push(timeString);
      }
    };
    
    // Función para actualizar el gráfico de usuarios activos en tiempo real
    const updateActiveUsersChart = () => {
      if (apexChartInstance) {
        const lastValue = activeUsersData[activeUsersData.length - 1];
        const now = new Date();
        const hour = now.getHours();
        
        let baseTrend = 0;
        
        if (hour >= 17 && hour < 23) {
          baseTrend = 15;
        } 
        else if (hour < 8) {
          baseTrend = -10;
        } 
        else {
          baseTrend = 5;
        }
        
        const randomFactor = Math.random();
        let variation;
        
        if (randomFactor < 0.2) {
          variation = Math.floor(Math.random() * -80) - 30;
        } else if (randomFactor < 0.4) {
          variation = Math.floor(Math.random() * 80) + 30;
        } else {
          variation = Math.floor(Math.random() * 40) - 20;
        }
        
        const newValue = Math.max(3000, Math.min(3500, lastValue + baseTrend + variation));
        activeUsers.value = newValue;
        
        const MAX_POINTS = 12;
        
        activeUsersData.push(newValue);
        
        if (activeUsersData.length > MAX_POINTS) {
          activeUsersData.shift();
        }
        
        const timeString = `${now.getHours()}:${now.getMinutes().toString().padStart(2, '0')}`;
        timeLabels.push(timeString);
        if (timeLabels.length > MAX_POINTS) {
          timeLabels.shift();
        }
        
        apexChartInstance.updateSeries([{
          data: activeUsersData
        }]);
        
        apexChartInstance.updateOptions({
          xaxis: {
            categories: timeLabels
          },
          chart: {
            animations: {
              enabled: true,
              easing: 'easeinout',
              speed: 800,
              animateGradually: {
                enabled: true,
                delay: 150
              },
              dynamicAnimation: {
                enabled: true,
                speed: 350
              }
            }
          }
        });
      }
    };
    
    onMounted(() => {
      setTimeout(() => {
        // 1. Inicializar Chart.js para el gráfico de evaluación en forma de estrella (radar)
        if (ratingChart.value) {
          const ctx = ratingChart.value.getContext('2d');
          
          // Datos de evaluación por aspectos (escala de 1-5)
          const aspectsData = {
            labels: ['Diseño', 'Accesibilidad', 'Eficiencia', 'Usabilidad'],
            datasets: [{
              label: 'Evaluación',
              data: [4.8, 4.2, 4.9, 4.6], // Puntuaciones de cada aspecto
              backgroundColor: 'rgba(240, 138, 36, 0.3)',
              borderColor: 'rgba(240, 138, 36, 0.8)',
              borderWidth: 3,
              pointBackgroundColor: 'rgba(240, 138, 36, 1)',
              pointBorderColor: '#fff',
              pointBorderWidth: 2,
              pointRadius: 6,
              pointHoverRadius: 8,
              pointHoverBackgroundColor: 'rgba(240, 138, 36, 1)',
              pointHoverBorderColor: '#fff',
              pointHoverBorderWidth: 3
            }]
          };
          
          chartJsRatingInstance = new Chart(ctx, {
            type: 'radar',
            data: aspectsData,
            options: {
              responsive: true,
              maintainAspectRatio: false,
              plugins: {
                legend: {
                  display: false
                },
                tooltip: {
                  backgroundColor: 'rgba(0, 0, 0, 0.8)',
                  titleColor: '#fff',
                  bodyColor: '#fff',
                  borderColor: '#F08A24',
                  borderWidth: 1,
                  callbacks: {
                    label: function(context) {
                      return `${context.label}: ${context.parsed.r}/5`;
                    }
                  }
                }
              },
              scales: {
                r: {
                  beginAtZero: true,
                  min: 0,
                  max: 5,
                  ticks: {
                    stepSize: 1,
                    color: '#666',
                    backdropColor: 'transparent',
                    font: {
                      size: 10
                    }
                  },
                  grid: {
                    color: 'rgba(255, 255, 255, 0.2)',
                    lineWidth: 1
                  },
                  angleLines: {
                    color: 'rgba(255, 255, 255, 0.2)',
                    lineWidth: 1
                  },
                  pointLabels: {
                    color: '#ccc',
                    font: {
                      size: 12,
                      weight: '500'
                    },
                    padding: 15
                  }
                }
              },
              elements: {
                line: {
                  tension: 0.1
                }
              },
              animation: {
                duration: 1500,
                easing: 'easeInOutQuart'
              }
            }
          });
        }
        
        // Inicializar etiquetas de tiempo
        initTimeLabels();

        // 2. Inicializar ApexCharts para el gráfico de usuarios activos EN TIEMPO REAL
        if (activeUsersChart.value) {
          activeUsersData.length = 0;
          const baseValue = 3200;
          for (let i = 0; i < 12; i++) {
            const randomVariation = Math.floor(Math.random() * 300) - 150;
            activeUsersData.push(baseValue + randomVariation);
          }
          
          const options = {
            series: [{
              name: 'Usuarios activos',
              data: activeUsersData
            }],
            chart: {
              type: 'area',
              height: '100%',
              toolbar: {
                show: false
              },
              background: 'transparent',
              foreColor: '#aaa',
              animations: {
                enabled: true,
                easing: 'easeinout',
                speed: 800,
                animateGradually: {
                  enabled: true,
                  delay: 150
                },
                dynamicAnimation: {
                  enabled: true,
                  speed: 350
                }
              }
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
            markers: {
              size: 4,
              colors: ['#F08A24'],
              strokeColors: '#fff',
              strokeWidth: 2,
              hover: {
                size: 6
              }
            },
            xaxis: {
              categories: timeLabels,
              labels: {
                style: {
                  colors: '#aaa'
                },
                rotate: -45,
                rotateAlways: false,
                hideOverlappingLabels: true,
                maxHeight: 50
              },
              tooltip: {
                enabled: false
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
              borderColor: 'rgba(255, 255, 255, 0.1)',
              padding: {
                top: 0,
                right: 10,
                bottom: 0,
                left: 10
              },
              xaxis: {
                lines: {
                  show: false
                }
              }
            },
            tooltip: {
              theme: 'dark',
              x: {
                show: true
              },
              y: {
                formatter: function(val) {
                  return val.toFixed(0) + ' usuarios';
                }
              },
              marker: {
                show: true
              }
            },
            responsive: [{
              breakpoint: 576,
              options: {
                chart: {
                  height: '200px'
                },
                markers: {
                  size: 3
                }
              }
            }]
          };
          
          apexChartInstance = new ApexCharts(activeUsersChart.value, options);
          apexChartInstance.render();
          
          initTimeLabels();
          updateActiveUsersChart();
        }
        
        // 3. Inicializar ECharts para el gráfico de tasa de conversión (BARRAS HORIZONTALES)
        if (conversionChart.value) {
          echartsInstance = echarts.init(conversionChart.value);
          
          const months = ['Ene', 'Feb', 'Mar', 'Abr', 'May', 'Jun', 'Jul', 'Ago', 'Sep', 'Oct'];
          const conversionData = [5.2, 5.5, 5.8, 6.2, 6.5, 6.8, 7.2, 7.5, 7.9, 8.3];
          const targetLine = 7.0; // Línea objetivo
          
          const option = {
            tooltip: {
              trigger: 'axis',
              backgroundColor: 'rgba(0, 0, 0, 0.8)',
              borderColor: '#F08A24',
              borderWidth: 1,
              textStyle: {
                color: '#fff'
              },
              formatter: function(params) {
                const dataPoint = params[0];
                const isAboveTarget = dataPoint.value >= targetLine;
                const status = isAboveTarget ? '✅ Objetivo superado' : '⚠️ Por debajo del objetivo';
                return `${dataPoint.name}: ${dataPoint.value}%<br/>
                        Objetivo: ${targetLine}%<br/>
                        ${status}`;
              }
            },
            grid: {
              top: 20,
              right: 30,
              bottom: 40,
              left: 50,
              containLabel: true
            },
            xAxis: {
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
                fontSize: 11,
                formatter: '{value}%'
              }
            },
            yAxis: {
              type: 'category',
              data: months,
              axisLine: {
                lineStyle: {
                  color: 'rgba(255, 255, 255, 0.2)'
                }
              },
              axisTick: {
                show: false
              },
              axisLabel: {
                color: '#aaa',
                fontSize: 11,
                margin: 8
              }
            },
            series: [
              {
                name: 'Tasa de conversión',
                type: 'bar',
                data: conversionData.map((value, index) => ({
                  value: value,
                  itemStyle: {
                    color: value >= targetLine 
                      ? new echarts.graphic.LinearGradient(0, 0, 1, 0, [
                          { offset: 0, color: '#F08A24' },
                          { offset: 1, color: '#FFB366' }
                        ])
                      : new echarts.graphic.LinearGradient(0, 0, 1, 0, [
                          { offset: 0, color: '#FF6B6B' },
                          { offset: 1, color: '#FF8E8E' }
                        ])
                  }
                })),
                barWidth: '60%',
                label: {
                  show: true,
                  position: 'right',
                  color: '#fff',
                  fontSize: 11,
                  fontWeight: 'bold',
                  formatter: '{c}%'
                },
                markLine: {
                  silent: true,
                  lineStyle: {
                    color: '#F08A24',
                    type: 'dashed',
                    width: 2
                  },
                  label: {
                    show: true,
                    position: 'end',
                    formatter: 'Objetivo: 7%',
                    color: '#F08A24',
                    fontSize: 11,
                    fontWeight: 'bold'
                  },
                  data: [
                    {
                      xAxis: targetLine
                    }
                  ]
                }
              }
            ],
            animation: true,
            animationDuration: 1000,
            animationEasing: 'cubicOut'
          };
          
          echartsInstance.setOption(option);
        }
        
        // 4. Inicializar ApexCharts para el gráfico de feedback como PIE CHART
        if (feedbackChart.value) {
          const options = {
            series: [90, 10],
            chart: {
              type: 'donut',
              height: '100%',
              toolbar: {
                show: false
              },
              background: 'transparent'
            },
            labels: ['Positivo', 'Negativo'],
            colors: ['#F08A24', '#FF4560'],
            plotOptions: {
              pie: {
                donut: {
                  size: '55%',
                  labels: {
                    show: true,
                    name: {
                      show: true,
                      fontSize: '14px',
                      fontWeight: 600,
                      color: '#fff',
                      offsetY: -10
                    },
                    value: {
                      show: true,
                      fontSize: '16px',
                      fontWeight: 400,
                      color: '#fff',
                      offsetY: 5,
                      formatter: function (val) {
                        return val + '%';
                      }
                    },
                    total: {
                      show: true,
                      label: 'Total',
                      fontSize: '14px',
                      fontWeight: 600,
                      color: '#fff',
                      formatter: function () {
                        return '427';
                      }
                    }
                  }
                }
              }
            },
            dataLabels: {
              enabled: false
            },
            legend: {
              position: 'bottom',
              horizontalAlign: 'center',
              fontSize: '12px',
              labels: {
                colors: '#aaa'
              },
              markers: {
                width: 12,
                height: 12,
                radius: 12
              },
              itemMargin: {
                horizontal: 10,
                vertical: 0
              }
            },
            stroke: {
              width: 0
            },
            tooltip: {
              theme: 'dark',
              y: {
                formatter: function(val) {
                  return val + '%';
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
        
        updateActiveUsersInterval = setInterval(updateActiveUsersChart, 2000);
      }, 300);
    });
    
    onUnmounted(() => {
      if (updateActiveUsersInterval) {
        clearInterval(updateActiveUsersInterval);
      }
      
      if (echartsInstance) {
        echartsInstance.dispose();
      }
      
      if (apexChartInstance) {
        apexChartInstance.destroy();
      }
      
      if (feedbackChartInstance) {
        feedbackChartInstance.destroy();
      }
      
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

/* Leyenda para el gráfico de evaluación */
.rating-legend {
  margin-top: 8px;
  display: flex;
  justify-content: center;
  align-items: center;
}

.legend-item {
  display: flex;
  align-items: center;
  gap: 6px;
  font-size: 12px;
  color: #aaa;
}

.legend-color {
  width: 12px;
  height: 12px;
  border-radius: 2px;
}

/* Indicador de estado para conversión */
.conversion-status {
  display: flex;
  align-items: center;
  gap: 6px;
}

.status-indicator {
  width: 8px;
  height: 8px;
  border-radius: 50%;
}

.status-indicator.success {
  background-color: #00E396;
  box-shadow: 0 0 6px rgba(0, 227, 150, 0.5);
}

.status-indicator.warning {
  background-color: #FEB019;
  box-shadow: 0 0 6px rgba(254, 176, 25, 0.5);
}

.status-text {
  font-size: 12px;
  color: #00E396;
  font-weight: 500;
}

/* Ajustes para pantallas más grandes */
@media (min-width: 768px) {
  .dashboard-card {
    height: 350px;
  }
}
</style>