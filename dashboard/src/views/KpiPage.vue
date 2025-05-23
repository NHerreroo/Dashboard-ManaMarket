<template>
  <ion-page>
    <ion-header :translucent="true">
      <ion-toolbar class="dark-toolbar">
        <ion-buttons slot="start">
          <ion-menu-button color="light"></ion-menu-button>
        </ion-buttons>
        <ion-title>🎯 KPIs</ion-title>
      </ion-toolbar>
    </ion-header>

    <ion-content class="dark-content">
      <div class="dashboard-container">
        <!-- KPIs de Negocio -->
        <div class="section-container">
          <h1 class="section-title">
            <span class="section-icon">🌍</span> KPIs de Negocio
            <div class="section-line"></div>
          </h1>
          
          <ion-accordion-group expand="inset" :multiple="true" class="dark-accordion">
            <ion-accordion value="1">
              <ion-item slot="header" class="accordion-header">
                <ion-icon slot="start" :icon="downloadOutline" class="accordion-icon"></ion-icon>
                <ion-label>Número de descargas</ion-label>
              </ion-item>
              <div class="accordion-content" slot="content">
                <p>Número total de instalaciones únicas de la app desde Google Play o App Store.</p>
                
                <div class="smart-methodology">
                  <h4>Metodología SMART</h4>
                  <div class="smart-item">
                    <span class="smart-letter">S</span>
                    <span class="smart-text">Específico: Contabilizar el número total de instalaciones únicas de la aplicación en las tiendas oficiales, actualmente en 12.458 descargas.</span>
                  </div>
                  <div class="smart-item">
                    <span class="smart-letter">M</span>
                    <span class="smart-text">Medible: Se mide a través de las consolas de desarrollador de Google Play y App Store, con un crecimiento mensual promedio de 250 descargas.</span>
                  </div>
                  <div class="smart-item">
                    <span class="smart-letter">A</span>
                    <span class="smart-text">Alcanzable: Objetivo de alcanzar 15.000 descargas para finales del trimestre, basado en la tendencia de crecimiento actual (2.400 en diciembre).</span>
                  </div>
                  <div class="smart-item">
                    <span class="smart-letter">R</span>
                    <span class="smart-text">Relevante: Indicador clave para medir la penetración en el mercado y el alcance de la aplicación, con impacto directo en los ingresos potenciales.</span>
                  </div>
                  <div class="smart-item">
                    <span class="smart-letter">T</span>
                    <span class="smart-text">Temporal: Evaluación mensual con objetivos trimestrales definidos. Próxima revisión: final del mes en curso.</span>
                  </div>
                </div>
              </div>
            </ion-accordion>
            
            <ion-accordion value="2">
              <ion-item slot="header" class="accordion-header">
                <ion-icon slot="start" :icon="starOutline" class="accordion-icon"></ion-icon>
                <ion-label>Valoración media de usuarios</ion-label>
              </ion-item>
              <div class="accordion-content" slot="content">
                <p>Puntuación promedio otorgada por los usuarios (de 1 a 5 estrellas).</p>
                
                <div class="smart-methodology">
                  <h4>Metodología SMART</h4>
                  <div class="smart-item">
                    <span class="smart-letter">S</span>
                    <span class="smart-text">Específico: Medir la satisfacción de los usuarios a través de sus valoraciones en las tiendas de aplicaciones, actualmente en 4.7/5 estrellas.</span>
                  </div>
                  <div class="smart-item">
                    <span class="smart-letter">M</span>
                    <span class="smart-text">Medible: Promedio ponderado de 1.815 valoraciones totales, con mayoría de 5 estrellas (1.200) y minoría de 1 estrella (15).</span>
                  </div>
                  <div class="smart-item">
                    <span class="smart-letter">A</span>
                    <span class="smart-text">Alcanzable: Mantener la valoración por encima de 4.5 estrellas mientras se incrementa el número total de valoraciones.</span>
                  </div>
                  <div class="smart-item">
                    <span class="smart-letter">R</span>
                    <span class="smart-text">Relevante: Impacta directamente en la visibilidad de la app y en la decisión de descarga de nuevos usuarios, siendo un factor clave en los algoritmos de las tiendas.</span>
                  </div>
                  <div class="smart-item">
                    <span class="smart-letter">T</span>
                    <span class="smart-text">Temporal: Revisión semanal de nuevas valoraciones y respuesta en menos de 48 horas a comentarios negativos para mantener la alta puntuación.</span>
                  </div>
                </div>
              </div>
            </ion-accordion>
            
            <ion-accordion value="3">
              <ion-item slot="header" class="accordion-header">
                <ion-icon slot="start" :icon="peopleOutline" class="accordion-icon"></ion-icon>
                <ion-label>Usuarios activos en creación de mazos</ion-label>
              </ion-item>
              <div class="accordion-content" slot="content">
                <p>Número de usuarios únicos que han usado la función de creación de mazos en un mes.</p>
                
                <div class="smart-methodology">
                  <h4>Metodología SMART</h4>
                  <div class="smart-item">
                    <span class="smart-letter">S</span>
                    <span class="smart-text">Específico: Contabilizar usuarios únicos que crean o modifican al menos un mazo mensualmente, actualmente 3.245 usuarios activos.</span>
                  </div>
                  <div class="smart-item">
                    <span class="smart-letter">M</span>
                    <span class="smart-text">Medible: A través de eventos de analytics implementados en la funcionalidad de creación de mazos, con monitoreo en tiempo real.</span>
                  </div>
                  <div class="smart-item">
                    <span class="smart-letter">A</span>
                    <span class="smart-text">Alcanzable: Lograr 3.500 usuarios activos en creación de mazos para el próximo mes, basado en la tendencia de crecimiento actual.</span>
                  </div>
                  <div class="smart-item">
                    <span class="smart-letter">R</span>
                    <span class="smart-text">Relevante: Indica el nivel de engagement con una de las funcionalidades core de la aplicación, correlacionado con la retención de usuarios.</span>
                  </div>
                  <div class="smart-item">
                    <span class="smart-letter">T</span>
                    <span class="smart-text">Temporal: Monitoreo en tiempo real con evaluación diaria de tendencias y campañas mensuales para incentivar el uso de la funcionalidad.</span>
                  </div>
                </div>
              </div>
            </ion-accordion>
            
            <ion-accordion value="4">
              <ion-item slot="header" class="accordion-header">
                <ion-icon slot="start" :icon="cartOutline" class="accordion-icon"></ion-icon>
                <ion-label>Tasa de conversión de compradores</ion-label>
              </ion-item>
              <div class="accordion-content" slot="content">
                <p>Porcentaje de usuarios registrados que han realizado al menos una compra.</p>
                
                <div class="smart-methodology">
                  <h4>Metodología SMART</h4>
                  <div class="smart-item">
                    <span class="smart-letter">S</span>
                    <span class="smart-text">Específico: Medir el porcentaje de usuarios que completan al menos una transacción de compra, actualmente en 8.3%.</span>
                  </div>
                  <div class="smart-item">
                    <span class="smart-letter">M</span>
                    <span class="smart-text">Medible: (Número de usuarios con al menos una compra / Total de usuarios registrados) × 100, con tendencia de crecimiento desde 5.2% en enero hasta 8.3% en octubre.</span>
                  </div>
                  <div class="smart-item">
                    <span class="smart-letter">A</span>
                    <span class="smart-text">Alcanzable: Incrementar la tasa de conversión hasta 9% para finales del año, superando el objetivo inicial de 7% (marcado en el gráfico).</span>
                  </div>
                  <div class="smart-item">
                    <span class="smart-letter">R</span>
                    <span class="smart-text">Relevante: Impacto directo en los ingresos y sostenibilidad financiera del proyecto, con un crecimiento constante de aproximadamente 0.3% mensual.</span>
                  </div>
                  <div class="smart-item">
                    <span class="smart-letter">T</span>
                    <span class="smart-text">Temporal: Análisis mensual con ajustes en la estrategia de monetización cada trimestre, próxima revisión a finales del mes en curso.</span>
                  </div>
                </div>
              </div>
            </ion-accordion>
            
            <ion-accordion value="5">
              <ion-item slot="header" class="accordion-header">
                <ion-icon slot="start" :icon="chatbubbleOutline" class="accordion-icon"></ion-icon>
                <ion-label>Número de respuestas de feedback</ion-label>
              </ion-item>
              <div class="accordion-content" slot="content">
                <p>Total de opiniones recibidas mediante encuestas sobre la función de escaneo.</p>
                
                <div class="smart-methodology">
                  <h4>Metodología SMART</h4>
                  <div class="smart-item">
                    <span class="smart-letter">S</span>
                    <span class="smart-text">Específico: Contabilizar las respuestas a encuestas específicas sobre la funcionalidad de escaneo, actualmente 427 respuestas totales.</span>
                  </div>
                  <div class="smart-item">
                    <span class="smart-letter">M</span>
                    <span class="smart-text">Medible: 427 respuestas totales con distribución de 90% positivas (384) y 10% negativas (43), según muestra el gráfico circular.</span>
                  </div>
                  <div class="smart-item">
                    <span class="smart-letter">A</span>
                    <span class="smart-text">Alcanzable: Obtener 500 respuestas totales para el próximo trimestre, manteniendo o mejorando el ratio de feedback positivo.</span>
                  </div>
                  <div class="smart-item">
                    <span class="smart-letter">R</span>
                    <span class="smart-text">Relevante: Proporciona insights directos para mejorar una funcionalidad diferenciadora de la app, con alta tasa de satisfacción actual.</span>
                  </div>
                  <div class="smart-item">
                    <span class="smart-letter">T</span>
                    <span class="smart-text">Temporal: Encuestas continuas con análisis mensual y aplicación de mejoras basadas en feedback negativo en ciclos de 6 semanas.</span>
                  </div>
                </div>
              </div>
            </ion-accordion>
          </ion-accordion-group>
        </div>
        
        <!-- KPIs Técnicos -->
        <div class="section-container">
          <h1 class="section-title">
            <span class="section-icon">⚙️</span> KPIs Técnicos
            <div class="section-line"></div>
          </h1>
          
          <ion-accordion-group expand="inset" :multiple="true" class="dark-accordion">
            <ion-accordion value="1">
              <ion-item slot="header" class="accordion-header">
                <ion-icon slot="start" :icon="timeOutline" class="accordion-icon"></ion-icon>
                <ion-label>Tiempo medio de respuesta</ion-label>
              </ion-item>
              <div class="accordion-content" slot="content">
                <p>Mide el tiempo promedio en milisegundos que tarda la app en mostrar resultados al buscar cartas.</p>
                
                <div class="smart-methodology">
                  <h4>Metodología SMART</h4>
                  <div class="smart-item">
                    <span class="smart-letter">S</span>
                    <span class="smart-text">Específico: Medir el tiempo desde que el usuario inicia una búsqueda hasta que se muestran los resultados, actualmente en 245ms.</span>
                  </div>
                  <div class="smart-item">
                    <span class="smart-letter">M</span>
                    <span class="smart-text">Medible: Promedio en milisegundos registrado por las herramientas de monitoreo, con fluctuaciones entre 230ms y 260ms en los últimos 10 minutos.</span>
                  </div>
                  <div class="smart-item">
                    <span class="smart-letter">A</span>
                    <span class="smart-text">Alcanzable: Mantener un tiempo de respuesta inferior a 250ms en el 95% de las búsquedas, con objetivo de reducir a 230ms de media.</span>
                  </div>
                  <div class="smart-item">
                    <span class="smart-letter">R</span>
                    <span class="smart-text">Relevante: Impacta directamente en la experiencia de usuario y en la percepción de calidad de la app, especialmente en usuarios que realizan múltiples búsquedas.</span>
                  </div>
                  <div class="smart-item">
                    <span class="smart-letter">T</span>
                    <span class="smart-text">Temporal: Monitoreo continuo con revisión cada 10 minutos y optimizaciones semanales del algoritmo de búsqueda.</span>
                  </div>
                </div>
              </div>
            </ion-accordion>
            
            <ion-accordion value="2">
              <ion-item slot="header" class="accordion-header">
                <ion-icon slot="start" :icon="pulseOutline" class="accordion-icon"></ion-icon>
                <ion-label>Disponibilidad del sistema (%)</ion-label>
              </ion-item>
              <div class="accordion-content" slot="content">
                <p>Porcentaje de tiempo mensual en que la app está operativa sin caídas.</p>
                
                <div class="smart-methodology">
                  <h4>Metodología SMART</h4>
                  <div class="smart-item">
                    <span class="smart-letter">S</span>
                    <span class="smart-text">Específico: Medir el tiempo en que los servicios backend y la app están completamente operativos, actualmente en 99.8%.</span>
                  </div>
                  <div class="smart-item">
                    <span class="smart-letter">M</span>
                    <span class="smart-text">Medible: (Tiempo total - Tiempo de inactividad) / Tiempo total × 100, con fluctuaciones entre 99.6% y 99.9% en la última hora.</span>
                  </div>
                  <div class="smart-item">
                    <span class="smart-letter">A</span>
                    <span class="smart-text">Alcanzable: Mantener una disponibilidad mínima del 99.8% con objetivo de alcanzar 99.9% estable para el próximo mes.</span>
                  </div>
                  <div class="smart-item">
                    <span class="smart-letter">R</span>
                    <span class="smart-text">Relevante: Garantiza la confiabilidad del servicio y previene la pérdida de usuarios por problemas técnicos, especialmente durante picos de uso.</span>
                  </div>
                  <div class="smart-item">
                    <span class="smart-letter">T</span>
                    <span class="smart-text">Temporal: Monitoreo en tiempo real con alertas instantáneas y revisión cada 5 minutos de los servicios críticos.</span>
                  </div>
                </div>
              </div>
            </ion-accordion>
            
            <ion-accordion value="3">
              <ion-item slot="header" class="accordion-header">
                <ion-icon slot="start" :icon="alertCircleOutline" class="accordion-icon"></ion-icon>
                <ion-label>Porcentaje de errores de escaneo</ion-label>
              </ion-item>
              <div class="accordion-content" slot="content">
                <p>Proporción de escaneos incorrectos en relación al total de cartas escaneadas.</p>
                
                <div class="smart-methodology">
                  <h4>Metodología SMART</h4>
                  <div class="smart-item">
                    <span class="smart-letter">S</span>
                    <span class="smart-text">Específico: Medir la precisión del sistema de reconocimiento de cartas mediante escaneo, actualmente con 2.3% de errores.</span>
                  </div>
                  <div class="smart-item">
                    <span class="smart-letter">M</span>
                    <span class="smart-text">Medible: (Número de escaneos fallidos / Total de escaneos realizados) × 100, con tendencia de mejora desde 3.8% en mayo hasta 2.3% en octubre.</span>
                  </div>
                  <div class="smart-item">
                    <span class="smart-letter">A</span>
                    <span class="smart-text">Alcanzable: Reducir la tasa de error por debajo del 2% para finales de año, continuando la tendencia de mejora actual.</span>
                  </div>
                  <div class="smart-item">
                    <span class="smart-letter">R</span>
                    <span class="smart-text">Relevante: Afecta directamente a la utilidad de una de las funciones principales y diferenciadoras de la app, con impacto en la satisfacción del usuario.</span>
                  </div>
                  <div class="smart-item">
                    <span class="smart-letter">T</span>
                    <span class="smart-text">Temporal: Evaluación mensual con mejoras en el algoritmo de reconocimiento cada dos meses, próxima actualización programada para finales de noviembre.</span>
                  </div>
                </div>
              </div>
            </ion-accordion>
            
            <ion-accordion value="4">
              <ion-item slot="header" class="accordion-header">
                <ion-icon slot="start" :icon="newspaperOutline" class="accordion-icon"></ion-icon>
                <ion-label>Tiempo de carga de noticias</ion-label>
              </ion-item>
              <div class="accordion-content" slot="content">
                <p>Tiempo medio (en segundos) que tarda en cargar la sección de noticias.</p>
                
                <div class="smart-methodology">
                  <h4>Metodología SMART</h4>
                  <div class="smart-item">
                    <span class="smart-letter">S</span>
                    <span class="smart-text">Específico: Medir el tiempo desde que se solicita la sección de noticias hasta que se muestra completamente, actualmente en 1.2 segundos.</span>
                  </div>
                  <div class="smart-item">
                    <span class="smart-letter">M</span>
                    <span class="smart-text">Medible: Tiempo promedio en segundos, con tendencia de mejora desde 1.8s a 1.2s en los últimos 10 minutos según el gráfico.</span>
                  </div>
                  <div class="smart-item">
                    <span class="smart-letter">A</span>
                    <span class="smart-text">Alcanzable: Mantener un tiempo de carga inferior a 1.5 segundos de forma consistente, con objetivo de estabilizarlo en 1.2s.</span>
                  </div>
                  <div class="smart-item">
                    <span class="smart-letter">R</span>
                    <span class="smart-text">Relevante: Impacta en la experiencia de usuario y en el consumo de contenido actualizado dentro de la app, afectando el tiempo de permanencia.</span>
                  </div>
                  <div class="smart-item">
                    <span class="smart-letter">T</span>
                    <span class="smart-text">Temporal: Monitoreo cada minuto con optimizaciones de caché y carga progresiva implementadas semanalmente.</span>
                  </div>
                </div>
              </div>
            </ion-accordion>
            
            <ion-accordion value="5">
              <ion-item slot="header" class="accordion-header">
                <ion-icon slot="start" :icon="saveOutline" class="accordion-icon"></ion-icon>
                <ion-label>Copias de seguridad completadas</ion-label>
              </ion-item>
              <div class="accordion-content" slot="content">
                <p>Porcentaje de días del mes en que se ha generado correctamente una copia de seguridad.</p>
                
                <div class="smart-methodology">
                  <h4>Metodología SMART</h4>
                  <div class="smart-item">
                    <span class="smart-letter">S</span>
                    <span class="smart-text">Específico: Verificar la correcta ejecución y validez de las copias de seguridad programadas, actualmente con un 96% de éxito.</span>
                  </div>
                  <div class="smart-item">
                    <span class="smart-letter">M</span>
                    <span class="smart-text">Medible: (Número de backups exitosos / Número de backups programados) × 100, representado en el gráfico radial con 96% completado.</span>
                  </div>
                  <div class="smart-item">
                    <span class="smart-letter">A</span>
                    <span class="smart-text">Alcanzable: Incrementar la tasa de éxito al 98% para el próximo mes mediante la implementación de verificaciones redundantes.</span>
                  </div>
                  <div class="smart-item">
                    <span class="smart-letter">R</span>
                    <span class="smart-text">Relevante: Garantiza la integridad de los datos y la capacidad de recuperación ante incidentes, protegiendo la información de los usuarios.</span>
                  </div>
                  <div class="smart-item">
                    <span class="smart-letter">T</span>
                    <span class="smart-text">Temporal: Verificación diaria automática con pruebas de restauración semanales para validar la integridad de los backups.</span>
                  </div>
                </div>
              </div>
            </ion-accordion>
          </ion-accordion-group>
        </div>
      </div>
    </ion-content>
  </ion-page>
</template>

<script setup lang="ts">
import { IonButtons, IonContent, IonHeader, IonMenuButton, IonPage, IonTitle, IonToolbar, IonAccordionGroup, IonAccordion, IonItem, IonLabel, IonIcon } from '@ionic/vue';
import { 
  downloadOutline, 
  starOutline, 
  peopleOutline, 
  cartOutline, 
  chatbubbleOutline,
  timeOutline,
  pulseOutline,
  alertCircleOutline,
  newspaperOutline,
  saveOutline
} from 'ionicons/icons';
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

/* Estilos para las secciones */
.section-container {
  margin-bottom: 30px;
}

.section-title {
  display: flex;
  align-items: center;
  color: #ffffff;
  font-size: 20px;
  font-weight: 600;
  margin: 24px 8px 16px 8px;
  position: relative;
}

.section-icon {
  margin-right: 10px;
  font-size: 22px;
}

.section-line {
  flex: 1;
  height: 1px;
  background: linear-gradient(to right, rgba(240, 138, 36, 0.5), rgba(240, 138, 36, 0));
  margin-left: 15px;
}

/* Estilos para los acordeones */
.dark-accordion {
  background: transparent;
  margin-bottom: 20px;
}

.accordion-header {
  --background: #1E1E1E;
  --color: #ffffff;
  border-radius: 10px;
  margin-bottom: 8px;
  --padding-start: 16px;
  --padding-end: 16px;
  --inner-padding-end: 16px;
}

.accordion-icon {
  color: #F08A24;
  font-size: 20px;
  margin-right: 12px;
}

.accordion-content {
  background: #1E1E1E;
  color: #cccccc;
  border-radius: 0 0 10px 10px;
  margin-bottom: 8px;
  font-size: 14px;
  line-height: 1.6;
  padding: 16px 20px;
}

.accordion-content p {
  margin-top: 0;
  margin-bottom: 16px;
}

/* Estilos para los acordeones colapsados y expandidos */
ion-accordion.accordion-collapsing ion-item[slot='header'],
ion-accordion.accordion-collapsed ion-item[slot='header'] {
  --background: #1E1E1E;
  --color: #ffffff;
}

ion-accordion.accordion-expanding ion-item[slot='header'],
ion-accordion.accordion-expanded ion-item[slot='header'] {
  --background: rgba(240, 138, 36, 0.15);
  --color: #ffffff;
  border-left: 4px solid #F08A24;
}

/* Efecto hover para los elementos del acordeón */
ion-item.accordion-header:hover {
  --background: #2a2a2a;
}

/* Ajustes para el grupo de acordeones */
ion-accordion-group {
  margin: 0 8px;
}

/* Estilos para la metodología SMART */
.smart-methodology {
  background: rgba(240, 138, 36, 0.05);
  border-radius: 8px;
  padding: 16px;
  margin-top: 16px;
  border-left: 3px solid #F08A24;
}

.smart-methodology h4 {
  color: #F08A24;
  margin-top: 0;
  margin-bottom: 12px;
  font-size: 16px;
  font-weight: 600;
}

.smart-item {
  display: flex;
  margin-bottom: 10px;
  align-items: flex-start;
}

.smart-letter {
  background: #F08A24;
  color: #000;
  font-weight: 700;
  width: 24px;
  height: 24px;
  border-radius: 50%;
  display: flex;
  align-items: center;
  justify-content: center;
  margin-right: 12px;
  flex-shrink: 0;
}

.smart-text {
  flex: 1;
  color: #ddd;
  font-size: 14px;
  line-height: 1.5;
}

/* Ajustes para pantallas más grandes */
@media (min-width: 768px) {
  .dashboard-container {
    padding: 24px;
  }
  
  .section-title {
    font-size: 24px;
    margin: 32px 16px 20px 16px;
  }
  
  .section-icon {
    font-size: 26px;
  }
  
  .accordion-content {
    font-size: 15px;
    padding: 20px 24px;
  }
  
  ion-accordion-group {
    margin: 0 16px;
  }
  
  .smart-methodology {
    padding: 20px;
  }
  
  .smart-methodology h4 {
    font-size: 18px;
  }
  
  .smart-text {
    font-size: 15px;
  }
}
</style>