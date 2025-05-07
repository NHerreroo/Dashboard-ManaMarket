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
              </div>
            </ion-accordion>
            
            <ion-accordion value="2">
              <ion-item slot="header" class="accordion-header">
                <ion-icon slot="start" :icon="starOutline" class="accordion-icon"></ion-icon>
                <ion-label>Valoración media de usuarios</ion-label>
              </ion-item>
              <div class="accordion-content" slot="content">
                <p>Puntuación promedio otorgada por los usuarios (de 1 a 5 estrellas).</p>
              </div>
            </ion-accordion>
            
            <ion-accordion value="3">
              <ion-item slot="header" class="accordion-header">
                <ion-icon slot="start" :icon="peopleOutline" class="accordion-icon"></ion-icon>
                <ion-label>Usuarios activos en creación de mazos</ion-label>
              </ion-item>
              <div class="accordion-content" slot="content">
                <p>Número de usuarios únicos que han usado la función de creación de mazos en un mes.</p>
              </div>
            </ion-accordion>
            
            <ion-accordion value="4">
              <ion-item slot="header" class="accordion-header">
                <ion-icon slot="start" :icon="cartOutline" class="accordion-icon"></ion-icon>
                <ion-label>Tasa de conversión de compradores</ion-label>
              </ion-item>
              <div class="accordion-content" slot="content">
                <p>Porcentaje de usuarios registrados que han realizado al menos una compra.</p>
              </div>
            </ion-accordion>
            
            <ion-accordion value="5">
              <ion-item slot="header" class="accordion-header">
                <ion-icon slot="start" :icon="chatbubbleOutline" class="accordion-icon"></ion-icon>
                <ion-label>Número de respuestas de feedback</ion-label>
              </ion-item>
              <div class="accordion-content" slot="content">
                <p>Total de opiniones recibidas mediante encuestas sobre la función de escaneo.</p>
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
              </div>
            </ion-accordion>
            
            <ion-accordion value="2">
              <ion-item slot="header" class="accordion-header">
                <ion-icon slot="start" :icon="pulseOutline" class="accordion-icon"></ion-icon>
                <ion-label>Disponibilidad del sistema (%)</ion-label>
              </ion-item>
              <div class="accordion-content" slot="content">
                <p>Porcentaje de tiempo mensual en que la app está operativa sin caídas.</p>
              </div>
            </ion-accordion>
            
            <ion-accordion value="3">
              <ion-item slot="header" class="accordion-header">
                <ion-icon slot="start" :icon="alertCircleOutline" class="accordion-icon"></ion-icon>
                <ion-label>Porcentaje de errores de escaneo</ion-label>
              </ion-item>
              <div class="accordion-content" slot="content">
                <p>Proporción de escaneos incorrectos en relación al total de cartas escaneadas.</p>
              </div>
            </ion-accordion>
            
            <ion-accordion value="4">
              <ion-item slot="header" class="accordion-header">
                <ion-icon slot="start" :icon="newspaperOutline" class="accordion-icon"></ion-icon>
                <ion-label>Tiempo de carga de noticias</ion-label>
              </ion-item>
              <div class="accordion-content" slot="content">
                <p>Tiempo medio (en segundos) que tarda en cargar la sección de noticias.</p>
              </div>
            </ion-accordion>
            
            <ion-accordion value="5">
              <ion-item slot="header" class="accordion-header">
                <ion-icon slot="start" :icon="saveOutline" class="accordion-icon"></ion-icon>
                <ion-label>Copias de seguridad completadas</ion-label>
              </ion-item>
              <div class="accordion-content" slot="content">
                <p>Porcentaje de días del mes en que se ha generado correctamente una copia de seguridad.</p>
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
  margin-bottom: 0;
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
}
</style>