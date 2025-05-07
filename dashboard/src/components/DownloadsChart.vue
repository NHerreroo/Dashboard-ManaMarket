<template>
    <div class="chart-container">
      <h3>Número de descargas</h3>
      <div class="kpi-value">{{ currentValue }}<span class="unit">descargas</span></div>
      <div ref="chartContainer" class="chart-wrapper"></div>
    </div>
  </template>
  
  <script>
  import { defineComponent, ref, onMounted, onUnmounted, watch } from 'vue';
  
  export default defineComponent({
    name: 'DownloadsChart',
    props: {
      data: {
        type: Array,
        required: true
      },
      currentValue: {
        type: Number,
        required: true
      }
    },
    setup(props) {
      const chartContainer = ref(null);
      
      onMounted(() => {
        renderChart();
        window.addEventListener('resize', renderChart);
      });
      
      onUnmounted(() => {
        window.removeEventListener('resize', renderChart);
      });
      
      watch(() => props.data, () => {
        renderChart();
      }, { deep: true });
      
      const renderChart = () => {
        if (!chartContainer.value) return;
        
        // Limpiar el contenedor
        chartContainer.value.innerHTML = '';
        
        // Obtener dimensiones del contenedor
        const containerWidth = chartContainer.value.clientWidth;
        const containerHeight = chartContainer.value.clientHeight;
        
        // Calcular valores máximos para escalar el gráfico
        const maxValue = Math.max(...props.data.map(item => item.value)) * 1.1;
        
        // Calcular dimensiones de las barras
        const barWidth = containerWidth / props.data.length - 8;
        const barMargin = 4;
        
        // Crear SVG
        const svg = document.createElementNS('http://www.w3.org/2000/svg', 'svg');
        svg.setAttribute('width', containerWidth);
        svg.setAttribute('height', containerHeight);
        svg.style.overflow = 'visible';
        
        // Crear grupo para las barras
        const barsGroup = document.createElementNS('http://www.w3.org/2000/svg', 'g');
        
        // Crear barras
        props.data.forEach((item, index) => {
          // Calcular altura de la barra
          const barHeight = (item.value / maxValue) * containerHeight;
          const x = index * (barWidth + barMargin * 2);
          const y = containerHeight - barHeight;
          
          // Crear gradiente
          const gradientId = `gradient-${index}`;
          const gradient = document.createElementNS('http://www.w3.org/2000/svg', 'linearGradient');
          gradient.setAttribute('id', gradientId);
          gradient.setAttribute('x1', '0%');
          gradient.setAttribute('y1', '0%');
          gradient.setAttribute('x2', '0%');
          gradient.setAttribute('y2', '100%');
          
          const stop1 = document.createElementNS('http://www.w3.org/2000/svg', 'stop');
          stop1.setAttribute('offset', '0%');
          stop1.setAttribute('stop-color', 'rgba(240, 138, 36, 0.9)');
          
          const stop2 = document.createElementNS('http://www.w3.org/2000/svg', 'stop');
          stop2.setAttribute('offset', '100%');
          stop2.setAttribute('stop-color', 'rgba(240, 138, 36, 0.3)');
          
          gradient.appendChild(stop1);
          gradient.appendChild(stop2);
          
          const defs = document.createElementNS('http://www.w3.org/2000/svg', 'defs');
          defs.appendChild(gradient);
          svg.appendChild(defs);
          
          // Crear barra
          const bar = document.createElementNS('http://www.w3.org/2000/svg', 'rect');
          bar.setAttribute('x', x);
          bar.setAttribute('y', y);
          bar.setAttribute('width', barWidth);
          bar.setAttribute('height', barHeight);
          bar.setAttribute('rx', '4');
          bar.setAttribute('ry', '4');
          bar.setAttribute('fill', `url(#${gradientId})`);
          
          // Crear tooltip al pasar el mouse
          bar.addEventListener('mouseover', (e) => {
            const tooltip = document.createElement('div');
            tooltip.className = 'chart-tooltip';
            tooltip.textContent = `${item.month}: ${item.value} descargas`;
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
            
            bar.addEventListener('mousemove', (e) => {
              tooltip.style.left = `${e.pageX + 10}px`;
              tooltip.style.top = `${e.pageY - 30}px`;
            });
            
            bar.addEventListener('mouseout', () => {
              document.body.removeChild(tooltip);
            });
          });
          
          barsGroup.appendChild(bar);
          
          // Añadir etiqueta del mes
          if (index % 2 === 0) { // Mostrar cada dos meses para evitar solapamiento
            const text = document.createElementNS('http://www.w3.org/2000/svg', 'text');
            text.setAttribute('x', x + barWidth / 2);
            text.setAttribute('y', containerHeight + 15);
            text.setAttribute('text-anchor', 'middle');
            text.setAttribute('fill', '#aaa');
            text.setAttribute('font-size', '10px');
            text.textContent = item.month;
            svg.appendChild(text);
          }
        });
        
        // Añadir líneas de referencia horizontales
        for (let i = 0; i <= 4; i++) {
          const y = containerHeight - (containerHeight * (i / 4));
          
          const line = document.createElementNS('http://www.w3.org/2000/svg', 'line');
          line.setAttribute('x1', '0');
          line.setAttribute('y1', y);
          line.setAttribute('x2', containerWidth);
          line.setAttribute('y2', y);
          line.setAttribute('stroke', 'rgba(255, 255, 255, 0.1)');
          line.setAttribute('stroke-width', '1');
          svg.appendChild(line);
          
          // Añadir valor de referencia
          const value = Math.round(maxValue * (i / 4));
          const text = document.createElementNS('http://www.w3.org/2000/svg', 'text');
          text.setAttribute('x', '-5');
          text.setAttribute('y', y + 4);
          text.setAttribute('text-anchor', 'end');
          text.setAttribute('fill', '#aaa');
          text.setAttribute('font-size', '10px');
          text.textContent = value;
          svg.appendChild(text);
        }
        
        svg.appendChild(barsGroup);
        chartContainer.value.appendChild(svg);
      };
      
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
  
  .chart-wrapper {
    flex: 1;
    width: 100%;
    position: relative;
    min-height: 150px;
  }
  </style>
  