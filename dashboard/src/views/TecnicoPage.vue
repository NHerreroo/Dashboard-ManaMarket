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
                <div class="chart-container-with-objective">
                  <div 
                    class="objective-icon"
                    @mouseenter="showObjectiveTooltip('response')"
                    @mouseleave="hideObjectiveTooltip"
                  >
                    <span v-if="isResponseTimeObjectiveMet" class="icon-success">✅</span>
                    <span v-else class="icon-pending">⏳</span>
                  </div>
                  <div 
                    v-if="tooltipVisible === 'response'" 
                    class="objective-tooltip"
                  >
                    <div class="tooltip-content">
                      <strong>Objetivo: Reducir a 230ms próximo mes</strong>
                      <div>Actual: {{ currentResponseTime }}ms</div>
                      <div>Reducir: {{ Math.max(0, currentResponseTime - 230) }}ms</div>
                      <div :class="isResponseTimeObjectiveMet ? 'status-success' : 'status-pending'">
                        {{ isResponseTimeObjectiveMet ? '✅ Objetivo cumplido' : '⏳ En progreso' }}
                      </div>
                    </div>
                  </div>
                  <response-time-chart 
                    title="Tiempo medio de respuesta"
                    :data="responseTimeData" 
                    :current-value="currentResponseTime"
                    :maxValue="300"
                    unit="ms"
                  />
                </div>
              </div>
            </ion-col>
            <ion-col size="12" size-md="6" size-lg="4">
              <div class="dashboard-card">
                <!-- 2. Disponibilidad del sistema -->
                <div class="chart-container-with-objective">
                  <div 
                    class="objective-icon"
                    @mouseenter="showObjectiveTooltip('availability')"
                    @mouseleave="hideObjectiveTooltip"
                  >
                    <span v-if="isAvailabilityObjectiveMet" class="icon-success">✅</span>
                    <span v-else class="icon-pending">⏳</span>
                  </div>
                  <div 
                    v-if="tooltipVisible === 'availability'" 
                    class="objective-tooltip"
                  >
                    <div class="tooltip-content">
                      <strong>Objetivo: Mantener 99.8% constante</strong>
                      <div>Actual: {{ systemAvailability }}%</div>
                      <div :class="isAvailabilityObjectiveMet ? 'status-success' : 'status-pending'">
                        {{ isAvailabilityObjectiveMet ? '✅ Objetivo cumplido' : '⚠️ Por debajo del objetivo' }}
                      </div>
                    </div>
                  </div>
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
              </div>
            </ion-col>
            <ion-col size="12" size-md="6" size-lg="4">
              <div class="dashboard-card">
                <!-- 3. Mapa de calor de escaneos de cartas MTG -->
                <div class="chart-container-with-objective">
                  <div 
                    class="objective-icon"
                    @mouseenter="showObjectiveTooltip('scan')"
                    @mouseleave="hideObjectiveTooltip"
                  >
                    <span v-if="isScanErrorObjectiveMet" class="icon-success">✅</span>
                    <span v-else class="icon-pending">⏳</span>
                  </div>
                  <div 
                    v-if="tooltipVisible === 'scan'" 
                    class="objective-tooltip"
                  >
                    <div class="tooltip-content">
                      <strong>Objetivo: Reducir tasa error al 2%</strong>
                      <strong>Para fin de año</strong>
                      <div>Actual: {{ scanErrorRate }}%</div>
                      <div>Reducir: {{ Math.max(0, (scanErrorRate - 2).toFixed(1)) }}%</div>
                      <div :class="isScanErrorObjectiveMet ? 'status-success' : 'status-pending'">
                        {{ isScanErrorObjectiveMet ? '✅ Objetivo cumplido' : '⏳ En progreso' }}
                      </div>
                    </div>
                  </div>
                  <div class="chart-container">
                    <h3>🃏 Mapa de escaneos de cartas</h3>
                    <div class="kpi-value">{{ scanErrorRate }}<span class="unit">%</span></div>
                    <div class="chart-wrapper">
                      <div ref="scanErrorChart" class="heatmap-chart"></div>
                    </div>
                    <div class="error-legend">
                      <div class="legend-item">
                        <span class="legend-color high"></span>
                        <span>Alto (&gt;5%)</span>
                      </div>
                      <div class="legend-item">
                        <span class="legend-color medium"></span>
                        <span>Medio (2-5%)</span>
                      </div>
                      <div class="legend-item">
                        <span class="legend-color low"></span>
                        <span>Bajo (&lt;2%)</span>
                      </div>
                    </div>
                  </div>
                </div>
              </div>
            </ion-col>
          </ion-row>

          <!-- Fila 2: Gráficos adicionales -->
          <ion-row>
            <ion-col size="12" size-md="8">
              <div class="dashboard-card">
                <!-- 4. Tiempo de carga de noticias (BARRAS) -->
                <div class="chart-container-with-objective">
                  <div 
                    class="objective-icon"
                    @mouseenter="showObjectiveTooltip('loading')"
                    @mouseleave="hideObjectiveTooltip"
                  >
                    <span v-if="isLoadingTimeObjectiveMet" class="icon-success">✅</span>
                    <span v-else class="icon-pending">⏳</span>
                  </div>
                  <div 
                    v-if="tooltipVisible === 'loading'" 
                    class="objective-tooltip"
                  >
                    <div class="tooltip-content">
                      <strong>Objetivo: Mantener ≤ 1.5s</strong>
                      <strong>Estabilizar en 1.2s próximos meses</strong>
                      <div>Actual: {{ loadingTime }}s</div>
                      <div>Meta futura: 1.2s</div>
                      <div :class="isLoadingTimeObjectiveMet ? 'status-success' : 'status-pending'">
                        {{ isLoadingTimeObjectiveMet ? '✅ Objetivo cumplido' : '⚠️ Por encima del objetivo' }}
                      </div>
                    </div>
                  </div>
                  <div class="chart-container">
                    <h3>Tiempo de carga de noticias (últimos 10 min)</h3>
                    <div class="kpi-value">{{ loadingTime }}<span class="unit">s</span></div>
                    <div class="chart-wrapper">
                      <div ref="newsLoadingChart"></div>
                    </div>
                    <div class="loading-info">
                      <div class="info-item">
                        <span class="info-label">Objetivo:</span>
                        <span class="info-value">&lt; 1.5s</span>
                      </div>
                      <div class="info-item">
                        <span class="info-label">Promedio:</span>
                        <span class="info-value">{{ loadingTime }}s</span>
                      </div>
                    </div>
                  </div>
                </div>
              </div>
            </ion-col>
            <ion-col size="12" size-md="4">
              <div class="dashboard-card">
                <!-- 5. Copias de seguridad completadas (CIRCULAR) -->
                <div class="chart-container-with-objective">
                  <div 
                    class="objective-icon"
                    @mouseenter="showObjectiveTooltip('backup')"
                    @mouseleave="hideObjectiveTooltip"
                  >
                    <span v-if="isBackupObjectiveMet" class="icon-success">✅</span>
                    <span v-else class="icon-pending">⏳</span>
                  </div>
                  <div 
                    v-if="tooltipVisible === 'backup'" 
                    class="objective-tooltip"
                  >
                    <div class="tooltip-content">
                      <strong>Objetivo: Incrementar tasa éxito a 98%</strong>
                      <div>Actual: {{ backupRate }}%</div>
                      <div>Incrementar: {{ Math.max(0, 98 - backupRate) }}%</div>
                      <div :class="isBackupObjectiveMet ? 'status-success' : 'status-pending'">
                        {{ isBackupObjectiveMet ? '✅ Objetivo cumplido' : '⏳ En progreso' }}
                      </div>
                    </div>
                  </div>
                  <div class="chart-container">
                    <h3>Copias de seguridad completadas</h3>
                    <div class="kpi-value">{{ backupRate }}<span class="unit">%</span></div>
                    <div class="chart-wrapper">
                      <div ref="backupChart" class="donut-chart-container"></div>
                    </div>
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
import { defineComponent, ref, onMounted, onUnmounted, nextTick, computed } from 'vue';
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
    const newsLoadingChart = ref(null);
    const backupChart = ref(null);
    
    // Datos para los KPIs
    const currentResponseTime = ref(245);
    const systemAvailability = ref(99.8);
    const scanErrorRate = ref(3.7);
    const loadingTime = ref(1.2);
    const backupRate = ref(96);
    
    // Estado del tooltip
    const tooltipVisible = ref(null);
    
    // Computed properties para verificar objetivos
    const isResponseTimeObjectiveMet = computed(() => currentResponseTime.value <= 230);
    const isAvailabilityObjectiveMet = computed(() => systemAvailability.value >= 99.8);
    const isScanErrorObjectiveMet = computed(() => scanErrorRate.value <= 2.0);
    const isLoadingTimeObjectiveMet = computed(() => loadingTime.value <= 1.5);
    const isBackupObjectiveMet = computed(() => backupRate.value >= 98);
    
    // Funciones para manejar tooltips
    const showObjectiveTooltip = (type) => {
      tooltipVisible.value = type;
    };
    
    const hideObjectiveTooltip = () => {
      tooltipVisible.value = null;
    };
    
    // Datos para los gráficos
    const responseTimeData = ref([230, 242, 255, 260, 248, 245, 238, 242, 250, 245]);
    const newsLoadingTimeData = ref([
      1.8, 1.7, 1.6, 1.5, 1.4, 1.3, 1.3, 1.2, 1.2, 1.2
    ]);
    
    // Variables para los gráficos
    let chartJsInstance = null;
    let heatmapChartInstance = null;
    let newsBarChartInstance = null;
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

    // Función para inicializar el mapa de calor de escaneos MTG
    const initHeatmapChart = () => {
      if (!scanErrorChart.value) return;

      try {
        heatmapChartInstance = echarts.init(scanErrorChart.value, 'dark');
        
        // Datos del mapa de calor para escaneos de cartas MTG: [x, y, valor]
        const mtgScanData = [
          // Fila 0: Dañada
          [0, 0, 6.2], [1, 0, 5.8], [2, 0, 7.1], [3, 0, 4.9], [4, 0, 5.5], [5, 0, 6.8],
          // Fila 1: Otro (problemas diversos)
          [0, 1, 2.3], [1, 1, 3.1], [2, 1, 2.8], [3, 1, 3.5], [4, 1, 2.9], [5, 1, 3.2],
          // Fila 2: Ángulo (perspectiva incorrecta)
          [0, 2, 4.1], [1, 2, 3.7], [2, 2, 4.8], [3, 2, 3.9], [4, 2, 4.3], [5, 2, 4.0],
          // Fila 3: Iluminación (problemas de luz)
          [0, 3, 1.8], [1, 3, 2.2], [2, 3, 1.5], [3, 3, 2.6], [4, 3, 1.9], [5, 3, 2.1]
        ];
        
        const option = {
          backgroundColor: 'transparent',
          tooltip: {
            position: 'top',
            backgroundColor: 'rgba(0, 0, 0, 0.9)',
            borderColor: '#F08A24',
            borderWidth: 1,
            textStyle: {
              color: '#fff',
              fontSize: 12
            },
            formatter: function(params) {
              const weeks = ['Sem 1', 'Sem 2', 'Sem 3', 'Sem 4', 'Sem 5', 'Sem 6'];
              const scanTypes = ['🔥 Dañada', '❓ Otro', '📐 Ángulo', '💡 Iluminación'];
              return `<strong>${scanTypes[params.data[1]]}</strong><br/>
                      ${weeks[params.data[0]]}: <span style="color: #F08A24">${params.data[2]}%</span> errores`;
            }
          },
          grid: {
            height: '60%',
            top: '10%',
            left: '20%',
            right: '10%',
            bottom: '30%'
          },
          xAxis: {
            type: 'category',
            data: ['Sem 1', 'Sem 2', 'Sem 3', 'Sem 4', 'Sem 5', 'Sem 6'],
            splitArea: {
              show: true,
              areaStyle: {
                color: ['rgba(255,255,255,0.02)', 'rgba(255,255,255,0.05)']
              }
            },
            axisLabel: {
              color: '#aaa',
              fontSize: 10,
              fontWeight: 'bold'
            },
            axisLine: {
              show: false
            },
            axisTick: {
              show: false
            },
            splitLine: {
              show: false
            }
          },
          yAxis: {
            type: 'category',
            data: ['🔥 Dañada', '❓ Otro', '📐 Ángulo', '💡 Iluminación'],
            splitArea: {
              show: true,
              areaStyle: {
                color: ['rgba(255,255,255,0.02)', 'rgba(255,255,255,0.05)']
              }
            },
            axisLabel: {
              color: '#aaa',
              fontSize: 10,
              fontWeight: 'bold',
              margin: 10
            },
            axisLine: {
              show: false
            },
            axisTick: {
              show: false
            },
            splitLine: {
              show: false
            }
          },
          visualMap: {
            min: 0,
            max: 8,
            calculable: false,
            orient: 'horizontal',
            left: 'center',
            bottom: '5%',
            textStyle: {
              color: '#aaa',
              fontSize: 9
            },
            inRange: {
              color: [
                '#0D4F3C', // Verde muy oscuro (valores bajos)
                '#2E7D32', // Verde oscuro
                '#4CAF50', // Verde
                '#8BC34A', // Verde claro
                '#CDDC39', // Lima
                '#FFEB3B', // Amarillo
                '#FFC107', // Ámbar
                '#FF9800', // Naranja
                '#FF5722', // Naranja rojizo
                '#F44336'  // Rojo (valores altos)
              ]
            },
            show: false
          },
          series: [{
            name: 'Errores de escaneo MTG',
            type: 'heatmap',
            data: mtgScanData,
            label: {
              show: true,
              color: '#fff',
              fontSize: 10,
              fontWeight: 'bold',
              formatter: function(params) {
                return params.data[2] + '%';
              },
              shadowColor: 'rgba(0, 0, 0, 0.5)',
              shadowBlur: 2
            },
            emphasis: {
              itemStyle: {
                shadowBlur: 10,
                shadowColor: 'rgba(240, 138, 36, 0.8)',
                borderColor: '#F08A24',
                borderWidth: 2
              },
              label: {
                fontSize: 11,
                fontWeight: 'bold'
              }
            },
            itemStyle: {
              borderColor: 'rgba(255, 255, 255, 0.1)',
              borderWidth: 1,
              borderRadius: 3
            }
          }]
        };
        
        heatmapChartInstance.setOption(option);
        
        // Forzar el resize después de un pequeño delay
        setTimeout(() => {
          if (heatmapChartInstance) {
            heatmapChartInstance.resize();
          }
        }, 100);
        
      } catch (error) {
        console.error('Error inicializando el mapa de calor:', error);
      }
    };
    
    onMounted(async () => {
      await nextTick();
      
      // Esperar un poco más para asegurar que el DOM esté completamente renderizado
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
        
        // 2. Inicializar ECharts para el mapa de calor de escaneos de cartas MTG
        initHeatmapChart();
        
        // 3. Inicializar ApexCharts para el gráfico de barras de tiempo de carga de noticias
        if (newsLoadingChart.value) {
          const options = {
            series: [{
              name: 'Tiempo de carga',
              data: newsLoadingTimeData.value
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
                columnWidth: '60%',
                dataLabels: {
                  position: 'top'
                }
              }
            },
            dataLabels: {
              enabled: true,
              formatter: function (val) {
                return val + 's';
              },
              offsetY: -20,
              style: {
                fontSize: '11px',
                colors: ['#fff'],
                fontWeight: 'bold'
              }
            },
            xaxis: {
              categories: ['Min 1', 'Min 2', 'Min 3', 'Min 4', 'Min 5', 'Min 6', 'Min 7', 'Min 8', 'Min 9', 'Min 10'],
              labels: {
                style: {
                  colors: '#aaa',
                  fontSize: '11px'
                }
              },
              axisBorder: {
                show: false
              },
              axisTicks: {
                show: false
              }
            },
            yaxis: {
              min: 0,
              max: 2.0,
              labels: {
                style: {
                  colors: '#aaa',
                  fontSize: '11px'
                },
                formatter: function (val) {
                  return val + 's';
                }
              }
            },
            grid: {
              borderColor: 'rgba(255, 255, 255, 0.1)',
              strokeDashArray: 3,
              xaxis: {
                lines: {
                  show: false
                }
              }
            },
            tooltip: {
              theme: 'dark',
              y: {
                formatter: function(val) {
                  return val + ' segundos';
                }
              }
            },
            annotations: {
              yaxis: [{
                y: 1.5,
                borderColor: '#FF4560',
                borderWidth: 2,
                strokeDashArray: 5,
                label: {
                  borderColor: '#FF4560',
                  style: {
                    color: '#fff',
                    background: '#FF4560',
                    fontSize: '10px'
                  },
                  text: 'Objetivo: 1.5s'
                }
              }]
            }
          };
          
          newsBarChartInstance = new ApexCharts(newsLoadingChart.value, options);
          newsBarChartInstance.render();
        }
        
        // 4. Inicializar ApexCharts para el gráfico circular de copias de seguridad
        if (backupChart.value) {
          const options = {
            series: [96],
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
          if (heatmapChartInstance) {
            heatmapChartInstance.resize();
          }
          if (newsBarChartInstance) {
            newsBarChartInstance.render();
          }
          if (donutChartInstance) {
            donutChartInstance.render();
          }
        };
        
        window.addEventListener('resize', handleResize);
        
        // Iniciar la actualización en tiempo real para el gráfico de disponibilidad
        updateInterval = setInterval(updateAvailabilityChart, 3000);
      }, 500);
    });
    
    onUnmounted(() => {
      // Limpiar el intervalo cuando el componente se desmonta
      if (updateInterval) {
        clearInterval(updateInterval);
      }
      
      // Destruir las instancias de los gráficos
      if (heatmapChartInstance) {
        heatmapChartInstance.dispose();
      }
      
      if (newsBarChartInstance) {
        newsBarChartInstance.destroy();
      }
      
      if (donutChartInstance) {
        donutChartInstance.destroy();
      }
      
      if (chartJsInstance) {
        chartJsInstance.destroy();
      }
      
      // Eliminar el event listener
      window.removeEventListener('resize', () => {});
    });
    
    return {
      currentResponseTime,
      systemAvailability,
      scanErrorRate,
      loadingTime,
      backupRate,
      responseTimeData,
      newsLoadingTimeData,
      availabilityChart,
      scanErrorChart,
      newsLoadingChart,
      backupChart,
      tooltipVisible,
      isResponseTimeObjectiveMet,
      isAvailabilityObjectiveMet,
      isScanErrorObjectiveMet,
      isLoadingTimeObjectiveMet,
      isBackupObjectiveMet,
      showObjectiveTooltip,
      hideObjectiveTooltip
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

/* Nuevo contenedor para gráficos con objetivos */
.chart-container-with-objective {
  position: relative;
  width: 100%;
  height: 100%;
  display: flex;
  flex-direction: column;
  overflow: hidden;
}

/* Icono de objetivo */
.objective-icon {
  position: absolute;
  top: 8px;
  right: 8px;
  z-index: 10;
  cursor: pointer;
  font-size: 18px;
  opacity: 0;
  transition: opacity 0.3s ease;
  background: rgba(0, 0, 0, 0.7);
  border-radius: 50%;
  width: 32px;
  height: 32px;
  display: flex;
  align-items: center;
  justify-content: center;
  backdrop-filter: blur(5px);
}

.chart-container-with-objective:hover .objective-icon {
  opacity: 1;
}

.icon-success {
  color: #00E396;
  filter: drop-shadow(0 0 4px rgba(0, 227, 150, 0.5));
}

.icon-pending {
  color: #FEB019;
  filter: drop-shadow(0 0 4px rgba(254, 176, 25, 0.5));
}

/* Tooltip de objetivo */
.objective-tooltip {
  position: absolute;
  top: 45px;
  right: 8px;
  z-index: 20;
  background: rgba(0, 0, 0, 0.95);
  border: 1px solid #F08A24;
  border-radius: 8px;
  padding: 12px;
  min-width: 200px;
  backdrop-filter: blur(10px);
  box-shadow: 0 4px 20px rgba(0, 0, 0, 0.5);
  animation: fadeInTooltip 0.2s ease-out;
}

@keyframes fadeInTooltip {
  from {
    opacity: 0;
    transform: translateY(-5px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

.tooltip-content {
  color: #fff;
  font-size: 12px;
  line-height: 1.4;
}

.tooltip-content strong {
  display: block;
  margin-bottom: 6px;
  color: #F08A24;
  font-size: 13px;
}

.tooltip-content div {
  margin-bottom: 3px;
}

.status-success {
  color: #00E396;
  font-weight: 600;
  margin-top: 6px;
}

.status-pending {
  color: #FEB019;
  font-weight: 600;
  margin-top: 6px;
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

/* Contenedor específico para el mapa de calor */
.heatmap-chart {
  width: 100%;
  height: 100%;
  min-height: 150px;
}

/* Contenedor para el gráfico circular */
.donut-chart-container {
  width: 100%;
  height: 100%;
}

/* Leyenda para el mapa de calor de errores */
.error-legend {
  display: flex;
  justify-content: space-around;
  margin-top: 8px;
  padding-top: 8px;
  border-top: 1px solid rgba(255, 255, 255, 0.1);
}

.legend-item {
  display: flex;
  align-items: center;
  gap: 4px;
  font-size: 10px;
  color: #aaa;
}

.legend-color {
  width: 10px;
  height: 10px;
  border-radius: 2px;
}

.legend-color.high {
  background-color: #F44336;
}

.legend-color.medium {
  background-color: #FF9800;
}

.legend-color.low {
  background-color: #2E7D32;
}

/* Información para el gráfico de tiempo de carga */
.loading-info {
  display: flex;
  justify-content: space-around;
  margin-top: 10px;
  padding-top: 10px;
  border-top: 1px solid rgba(255, 255, 255, 0.1);
}

.info-item {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 2px;
}

.info-label {
  font-size: 11px;
  color: #aaa;
}

.info-value {
  font-size: 13px;
  font-weight: 600;
  color: #F08A24;
}

/* Ajustes para pantallas más grandes */
@media (min-width: 768px) {
  .dashboard-card {
    height: 350px;
  }
}

/* Ajustes responsivos para tooltips */
@media (max-width: 768px) {
  .objective-tooltip {
    right: 8px;
    left: 8px;
    min-width: auto;
  }
}
</style>