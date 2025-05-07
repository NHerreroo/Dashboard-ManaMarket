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
        <h1 class="section-title">🌍 KPIs de Negocio</h1>
        <ion-accordion-group expand="inset" :multiple="true" class="dark-accordion">
          <ion-accordion value="1">
            <ion-item slot="header" class="accordion-header">
              <ion-label>Número de descargas</ion-label>
            </ion-item>
            <div class="ion-padding accordion-content" slot="content">Número total de instalaciones únicas de la app desde Google Play o App Store.</div>
          </ion-accordion>
          <ion-accordion value="2">
            <ion-item slot="header" class="accordion-header">
              <ion-label>Valoración media de usuarios</ion-label>
            </ion-item>
            <div class="ion-padding accordion-content" slot="content">Puntuación promedio otorgada por los usuarios (de 1 a 5 estrellas).</div>
          </ion-accordion>
          <ion-accordion value="3">
            <ion-item slot="header" class="accordion-header">
              <ion-label>Usuarios activos en creación de mazos</ion-label>
            </ion-item>
            <div class="ion-padding accordion-content" slot="content">Número de usuarios únicos que han usado la función de creación de mazos en un mes.</div>
          </ion-accordion>
          <ion-accordion value="4">
            <ion-item slot="header" class="accordion-header">
              <ion-label>Tasa de conversión de compradores</ion-label>
            </ion-item>
            <div class="ion-padding accordion-content" slot="content">Porcentaje de usuarios registrados que han realizado al menos una compra.</div>
          </ion-accordion>
          <ion-accordion value="5">
            <ion-item slot="header" class="accordion-header">
              <ion-label>Número de respuestas de feedback</ion-label>
            </ion-item>
            <div class="ion-padding accordion-content" slot="content">Total de opiniones recibidas mediante encuestas sobre la función de escaneo.</div>
          </ion-accordion>
        </ion-accordion-group>
        
        <h1 class="section-title">⚙️ KPIs Técnicos</h1>
        <ion-accordion-group expand="inset" :multiple="true" class="dark-accordion">
          <ion-accordion value="1">
            <ion-item slot="header" class="accordion-header">
              <ion-label>Tiempo medio de respuesta</ion-label>
            </ion-item>
            <div class="ion-padding accordion-content" slot="content">Mide el tiempo promedio en milisegundos que tarda la app en mostrar resultados al buscar cartas.</div>
          </ion-accordion>
          <ion-accordion value="2">
            <ion-item slot="header" class="accordion-header">
              <ion-label>Disponibilidad del sistema (%)</ion-label>
            </ion-item>
            <div class="ion-padding accordion-content" slot="content">Porcentaje de tiempo mensual en que la app está operativa sin caídas.</div>
          </ion-accordion>
          <ion-accordion value="3">
            <ion-item slot="header" class="accordion-header">
              <ion-label>Porcentaje de errores de escaneo</ion-label>
            </ion-item>
            <div class="ion-padding accordion-content" slot="content">Proporción de escaneos incorrectos en relación al total de cartas escaneadas.</div>
          </ion-accordion>
          <ion-accordion value="4">
            <ion-item slot="header" class="accordion-header">
              <ion-label>Tiempo de carga de noticias</ion-label>
            </ion-item>
            <div class="ion-padding accordion-content" slot="content">Tiempo medio (en segundos) que tarda en cargar la sección de noticias.</div>
          </ion-accordion>
          <ion-accordion value="5">
            <ion-item slot="header" class="accordion-header">
              <ion-label>Copias de seguridad completadas</ion-label>
            </ion-item>
            <div class="ion-padding accordion-content" slot="content">Porcentaje de días del mes en que se ha generado correctamente una copia de seguridad.</div>
          </ion-accordion>
        </ion-accordion-group>
      </div>
    </ion-content>
  </ion-page>
</template>

<script setup lang="ts">
import { IonButtons, IonContent, IonHeader, IonMenuButton, IonPage, IonTitle, IonToolbar, IonAccordionGroup, IonAccordion, IonItem, IonLabel } from '@ionic/vue';
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

.section-title {
  color: #ffffff;
  font-size: 24px;
  font-weight: 600;
  margin: 20px 0 15px 0;
  padding-left: 16px;
}

/* Estilos para los acordeones */
.dark-accordion {
  background: transparent;
  margin-bottom: 20px;
}

.accordion-header {
  --background: #1E1E1E;
  --color: #cccccc;
  border-radius: 8px;
  margin-bottom: 8px;
}

.accordion-content {
  background: #1E1E1E;
  color: #aaaaaa;
  border-radius: 0 0 8px 8px;
  margin-bottom: 8px;
  font-size: 14px;
  line-height: 1.5;
}

/* Estilos para los acordeones colapsados y expandidos */
ion-accordion.accordion-collapsing ion-item[slot='header'],
ion-accordion.accordion-collapsed ion-item[slot='header'] {
  --background: #1E1E1E;
  --color: #cccccc;
}

ion-accordion.accordion-expanding ion-item[slot='header'],
ion-accordion.accordion-expanded ion-item[slot='header'] {
  --background: rgba(240, 138, 36, 0.2);
  --color: #F08A24;
}

/* Efecto hover para los elementos del acordeón */
ion-item.accordion-header:hover {
  --background: #2a2a2a;
}

/* Ajustes para el grupo de acordeones */
ion-accordion-group {
  margin: 0 16px;
}

/* Ajustes para pantallas más grandes */
@media (min-width: 768px) {
  .section-title {
    font-size: 28px;
  }
  
  .accordion-content {
    font-size: 16px;
  }
}
</style>