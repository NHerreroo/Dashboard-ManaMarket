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
                <!-- 1. Tiempo medio de respuesta (ECHARTS) -->
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
                <!-- 2. Disponibilidad del sistema (CHART.JS BUBBLE CHART) -->
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
                <!-- 3. Errores de escaneo (APEXCHARTS TREEMAP) -->
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
                <!-- 4. Tiempo de carga de noticias (CUSTOM CHART) -->
                <div class="chart-container">
                  <h3>Tiempo de carga de noticias</h3>
                  <div class="kpi-value">{{ loadingTime }}<span class="unit">s</span></div>
                  <div ref="newsLoadingChart" class="chart-wrapper"></div>
                </div>
              </div>
            </ion-col>
            <ion-col size="12" size-md="4">
              <div class="dashboard-card">
                <!-- 5. Copias de seguridad completadas (APEXCHARTS POLAR AREA) -->
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
    const newsLoadingChart = ref(null);
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
    let scanErrorChartInstance = null;
    let newsLoadingChartInstance = null;
    let backupChartInstance = null;
    let updateInterval = null;
    
    // Datos para el gráfico de disponibilidad en tiempo real
    const availabilityData = [];
    const timeLabels = [];
    
    // Función para actualizar el gráfico de disponibilidad en tiempo real
    const updateAvailabilityChart = () => {
      if (chartJsInstance) {
        // Generar un nuevo valor aleatorio entre 99.5 y 100
        const newValue = (99.5 + Math.random() * 0.5).toFixed(1);
        systemAvailability.value = parseFloat(newValue);
        
        // Actualizar datos para el gráfico de burbujas
        const now = new Date();
        const hour = now.getHours();
        const minute = now.getMinutes();
        
        // Añadir nuevos puntos (máximo 20)
        availabilityData.push({
          x: hour + minute / 60, // Posición X basada en la hora (0-24)
          y: parseFloat(newValue), // Valor de disponibilidad
          r: Math.random() * 10 + 5 // Radio aleatorio entre 5 y 15
        });
        
        // Mantener solo los últimos 20 puntos
        if (availabilityData.length > 20) {
          availabilityData.shift();
        }
        
        // Actualizar el gráfico
        chartJsInstance.data.datasets[0].data = availabilityData;
        chartJsInstance.update();
      }
    };
    
    // Función para renderizar el gráfico personalizado de tiempo de carga
    const renderNewsLoadingChart = () => {
      if (!newsLoadingChart.value) return;
      
      // Limpiar el contenedor
      newsLoadingChart.value.innerHTML = '';
      
      // Obtener dimensiones del contenedor
      const containerWidth = newsLoadingChart.value.clientWidth;
      const containerHeight = newsLoadingChart.value.clientHeight;
      
      // Si no hay dimensiones, no renderizar
      if (containerWidth === 0 || containerHeight === 0) return;
      
      // Crear SVG
      const svg = document.createElementNS('http://www.w3.org/2000/svg', 'svg');
      svg.setAttribute('width', containerWidth);
      svg.setAttribute('height', containerHeight);
      svg.style.overflow = 'visible';
      
      // Definir márgenes
      const margin = { top: 20, right: 20, bottom: 30, left: 40 };
      const width = containerWidth - margin.left - margin.right;
      const height = containerHeight - margin.top - margin.bottom;
      
      // Crear grupo principal con transformación
      const g = document.createElementNS('http://www.w3.org/2000/svg', 'g');
      g.setAttribute('transform', `translate(${margin.left},${margin.top})`);
      
      // Calcular escalas
      const xScale = width / (newsLoadingTimeData.value.length - 1);
      const yScale = height / 3; // El máximo valor es 3 segundos
      
      // Crear líneas de referencia horizontales
      for (let i = 0; i <= 3; i++) {
        const y = height - i * yScale;
        
        const line = document.createElementNS('http://www.w3.org/2000/svg', 'line');
        line.setAttribute('x1', 0);
        line.setAttribute('y1', y);
        line.setAttribute('x2', width);
        line.setAttribute('y2', y);
        line.setAttribute('stroke', 'rgba(255, 255, 255, 0.1)');
        line.setAttribute('stroke-width', '1');
        g.appendChild(line);
        
        // Añadir etiqueta
        const text = document.createElementNS('http://www.w3.org/2000/svg', 'text');
        text.setAttribute('x', -10);
        text.setAttribute('y', y + 5);
        text.setAttribute('text-anchor', 'end');
        text.setAttribute('fill', '#aaa');
        text.setAttribute('font-size', '10px');
        text.textContent = i + 's';
        g.appendChild(text);
      }
      
      // Crear puntos de datos
      const points = [];
      newsLoadingTimeData.value.forEach((value, index) => {
        const x = index * xScale;
        const y = height - value * yScale;
        points.push(`${x},${y}`);
        
        // Crear círculo para cada punto
        const circle = document.createElementNS('http://www.w3.org/2000/svg', 'circle');
        circle.setAttribute('cx', x);
        circle.setAttribute('cy', y);
        circle.setAttribute('r', 4);
        circle.setAttribute('fill', '#F08A24');
        
        // Añadir tooltip al pasar el mouse
        circle.addEventListener('mouseover', (e) => {
          const tooltip = document.createElement('div');
          tooltip.className = 'chart-tooltip';
          tooltip.textContent = `${value}s`;
          tooltip.style.position = 'absolute';
          tooltip.style.left = `${e.pageX}px`;
          tooltip.style.top = `${e.pageY - 30}px`;
          tooltip.style.backgroundColor = 'rgba(0, 0, 0, 0.8)';
          tooltip.style.color = '#fff';
          tooltip.style.padding = '5px 10px';
          tooltip.style.borderRadius = '4px';
          tooltip.style.fontSize = '12px';
          tooltip.style.zIndex = '1000';
          tooltip.style.pointerEvents = 'none';
          document.body.appendChild(tooltip);
          
          circle.addEventListener('mousemove', (e) => {
            tooltip.style.left = `${e.pageX + 10}px`;
            tooltip.style.top = `${e.pageY - 30}px`;
          });
          
          circle.addEventListener('mouseout', () => {
            document.body.removeChild(tooltip);
          });
        });
        
        g.appendChild(circle);
      });
      
      // Crear línea de tendencia
      const linePath = document.createElementNS('http://www.w3.org/2000/svg', 'path');
      linePath.setAttribute('d', `M${points.join(' L')}`);
      linePath.setAttribute('fill', 'none');
      linePath.setAttribute('stroke', '#F08A24');
      linePath.setAttribute('stroke-width', '2');
      linePath.setAttribute('stroke-linecap', 'round');
      linePath.setAttribute('stroke-linejoin', 'round');
      g.appendChild(linePath);
      
      // Crear área bajo la línea
      const areaPath = document.createElementNS('http://www.w3.org/2000/svg', 'path');
      areaPath.setAttribute('d', `M0,${height} L${points.join(' L')} L${width},${height} Z`);
      areaPath.setAttribute('fill', 'url(#gradient)');
      
      // Crear gradiente
      const gradient = document.createElementNS('http://www.w3.org/2000/svg', 'linearGradient');
      gradient.setAttribute('id', 'gradient');
      gradient.setAttribute('x1', '0%');
      gradient.setAttribute('y1', '0%');
      gradient.setAttribute('x2', '0%');
      gradient.setAttribute('y2', '100%');
      
      const stop1 = document.createElementNS('http://www.w3.org/2000/svg', 'stop');
      stop1.setAttribute('offset', '0%');
      stop1.setAttribute('stop-color', 'rgba(240, 138, 36, 0.5)');
      
      const stop2 = document.createElementNS('http://www.w3.org/2000/svg', 'stop');
      stop2.setAttribute('offset', '100%');
      stop2.setAttribute('stop-color', 'rgba(240, 138, 36, 0.1)');
      
      gradient.appendChild(stop1);
      gradient.appendChild(stop2);
      
      const defs = document.createElementNS('http://www.w3.org/2000/svg', 'defs');
      defs.appendChild(gradient);
      svg.appendChild(defs);
      
      // Añadir área bajo la línea (después del gradiente)
      g.appendChild(areaPath);
      
      // Añadir etiquetas del eje X
      newsLoadingTimeData.value.forEach((_, index) => {
        if (index % 2 === 0) { // Mostrar cada dos puntos
          const x = index * xScale;
          const text = document.createElementNS('http://www.w3.org/2000/svg', 'text');
          text.setAttribute('x', x);
          text.setAttribute('y', height + 20);
          text.setAttribute('text-anchor', 'middle');
          text.setAttribute('fill', '#aaa');
          text.setAttribute('font-size', '10px');
          text.textContent = `Día ${index + 1}`;
          g.appendChild(text);
        }
      });
      
      // Añadir grupo al SVG
      svg.appendChild(g);
      
      // Añadir SVG al contenedor
      newsLoadingChart.value.appendChild(svg);
    };
    
    onMounted(() => {
      // Asegurarse de que los contenedores de gráficos tengan el tamaño correcto
      setTimeout(() => {
        // 1. Inicializar Chart.js para el gráfico de disponibilidad (BUBBLE CHART)
        if (availabilityChart.value) {
          const ctx = availabilityChart.value.getContext('2d');
          
          // Generar datos iniciales para el gráfico de burbujas
          for (let i = 0; i < 20; i++) {
            const hour = Math.floor(Math.random() * 24);
            const minute = Math.floor(Math.random() * 60);
            const value = (99.5 + Math.random() * 0.5).toFixed(1);
            
            availabilityData.push({
              x: hour + minute / 60,
              y: parseFloat(value),
              r: Math.random() * 10 + 5
            });
          }
          
          chartJsInstance = new Chart(ctx, {
            type: 'bubble',
            data: {
              datasets: [{
                label: 'Disponibilidad',
                data: availabilityData,
                backgroundColor: function(context) {
                  const value = context.raw.y;
                  const alpha = (value - 99.5) * 2; // 0.0 a 1.0 para valores entre 99.5 y 100
                  return `rgba(240, 138, 36, ${alpha})`;
                },
                borderColor: '#F08A24',
                borderWidth: 1
              }]
            },
            options: {
              responsive: true,
              maintainAspectRatio: false,
              scales: {
                y: {
                  min: 99.5,
                  max: 100,
                  grid: {
                    color: 'rgba(255, 255, 255, 0.1)'
                  },
                  ticks: {
                    color: '#aaa',
                    callback: function(value) {
                      return value + '%';
                    }
                  }
                },
                x: {
                  min: 0,
                  max: 24,
                  grid: {
                    color: 'rgba(255, 255, 255, 0.1)'
                  },
                  ticks: {
                    color: '#aaa',
                    callback: function(value) {
                      const hour = Math.floor(value);
                      const minute = Math.round((value - hour) * 60);
                      return `${hour}:${minute.toString().padStart(2, '0')}`;
                    }
                  }
                }
              },
              plugins: {
                legend: {
                  display: false
                },
                tooltip: {
                  callbacks: {
                    label: function(context) {
                      const x = context.raw.x;
                      const hour = Math.floor(x);
                      const minute = Math.round((x - hour) * 60);
                      const timeStr = `${hour}:${minute.toString().padStart(2, '0')}`;
                      return `Hora: ${timeStr}, Disponibilidad: ${context.raw.y}%`;
                    }
                  }
                }
              }
            }
          });
        }
        
        // 2. Inicializar ApexCharts para el gráfico de errores de escaneo (TREEMAP)
        if (scanErrorChart.value) {
          const data = [
            {
              name: 'Errores de escaneo',
              data: [
                {
                  x: 'Iluminación',
                  y: 1.2
                },
                {
                  x: 'Ángulo',
                  y: 0.5
                },
                {
                  x: 'Distancia',
                  y: 0.3
                },
                {
                  x: 'Reflejo',
                  y: 0.2
                },
                {
                  x: 'Otros',
                  y: 0.1
                }
              ]
            }
          ];
          
          const options = {
            series: data,
            chart: {
              type: 'treemap',
              height: '100%',
              toolbar: {
                show: false
              },
              background: 'transparent',
              foreColor: '#aaa'
            },
            colors: ['#F08A24'],
            plotOptions: {
              treemap: {
                distributed: true,
                enableShades: true,
                shadeIntensity: 0.5
              }
            },
            dataLabels: {
              enabled: true,
              style: {
                fontSize: '12px',
                fontWeight: 'bold',
                colors: ['#fff']
              },
              formatter: function(text, op) {
                return [text, op.value + '%'];
              }
            },
            legend: {
              show: false
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
          
          scanErrorChartInstance = new ApexCharts(scanErrorChart.value, options);
          scanErrorChartInstance.render();
        }
        
        // 3. Renderizar gráfico personalizado para tiempo de carga de noticias
        renderNewsLoadingChart();
        
        // 4. Inicializar ApexCharts para el gráfico de copias de seguridad (POLAR AREA)
        if (backupChart.value) {
          const options = {
            series: [25, 15, 44, 12],
            chart: {
              type: 'polarArea',
              height: '100%',
              toolbar: {
                show: false
              },
              background: 'transparent'
            },
            labels: ['Completas', 'Parciales', 'Incrementales', 'Fallidas'],
            fill: {
              opacity: 0.8
            },
            stroke: {
              width: 1,
              colors: ['#1E1E1E']
            },
            yaxis: {
              show: false
            },
            legend: {
              position: 'bottom',
              fontSize: '10px',
              labels: {
                colors: '#aaa'
              },
              markers: {
                width: 8,
                height: 8
              }
            },
            plotOptions: {
              polarArea: {
                rings: {
                  strokeWidth: 0
                },
                spokes: {
                  strokeWidth: 0
                }
              }
            },
            colors: ['#F08A24', '#FFAB00', '#FF4560', '#775DD0'],
            tooltip: {
              theme: 'dark',
              y: {
                formatter: function(val) {
                  return val + '%';
                }
              }
            }
          };
          
          backupChartInstance = new ApexCharts(backupChart.value, options);
          backupChartInstance.render();
        }
        
        // Manejar el redimensionamiento para todos los gráficos
        const handleResize = () => {
          if (scanErrorChartInstance) {
            scanErrorChartInstance.render();
          }
          if (backupChartInstance) {
            backupChartInstance.render();
          }
          renderNewsLoadingChart();
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
      if (scanErrorChartInstance) {
        scanErrorChartInstance.destroy();
      }
      
      if (backupChartInstance) {
        backupChartInstance.destroy();
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
      newsLoadingChart,
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