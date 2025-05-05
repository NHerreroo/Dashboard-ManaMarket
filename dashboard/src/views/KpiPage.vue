<template>
    <ion-page>
      <ion-header :translucent="true">
        <ion-toolbar>
          <ion-buttons slot="start">
            <ion-menu-button color="primary"></ion-menu-button>
          </ion-buttons>
          <ion-title>🎯 KPIs</ion-title>
        </ion-toolbar>
      </ion-header>
  
  
      <ion-content :fullscreen="true" class="ion-padding">
        <ion-header collapse="condense">
          <ion-toolbar>
            <ion-title size="large">🎯 KPIs</ion-title>
          </ion-toolbar>
        </ion-header>
  
  
        <h1 class="ion-padding">🌍 KPIs de Negocio</h1>
        <ion-accordion-group expand="inset" :multiple="true">
          <ion-accordion value="1">
            <ion-item slot="header">
              <ion-label>Número de descargas</ion-label>
            </ion-item>
            <div class="ion-padding" slot="content">Número total de instalaciones únicas de la app desde Google Play o App Store.</div>
          </ion-accordion>
          <ion-accordion value="2">
            <ion-item slot="header">
              <ion-label>Valoración media de usuarios</ion-label>
            </ion-item>
            <div class="ion-padding" slot="content">Puntuación promedio otorgada por los usuarios (de 1 a 5 estrellas).</div>
          </ion-accordion>
          <ion-accordion value="3">
            <ion-item slot="header">
              <ion-label>Usuarios activos en creación de mazos</ion-label>
            </ion-item>
            <div class="ion-padding" slot="content">Número de usuarios únicos que han usado la función de creación de mazos en un mes.</div>
          </ion-accordion>
          <ion-accordion value="4">
            <ion-item slot="header">
              <ion-label>Tasa de conversión de compradores</ion-label>
            </ion-item>
            <div class="ion-padding" slot="content">Porcentaje de usuarios registrados que han realizado al menos una compra.</div>
          </ion-accordion>
          <ion-accordion value="5">
            <ion-item slot="header">
              <ion-label>Número de respuestas de feedback</ion-label>
            </ion-item>
            <div class="ion-padding" slot="content">Total de opiniones recibidas mediante encuestas sobre la función de escaneo.</div>
          </ion-accordion>
        </ion-accordion-group>
        <br>
        <h1 class="ion-padding">⚙️ KPIs Técnicos</h1>
        <ion-accordion-group expand="inset" :multiple="true">
          <ion-accordion value="1">
            <ion-item slot="header" >
              <ion-label>Tiempo medio de respuesta</ion-label>
            </ion-item>
            <div class="ion-padding" slot="content">Mide el tiempo promedio en milisegundos que tarda la app en mostrar resultados al buscar cartas.</div>
          </ion-accordion>
          <ion-accordion value="2">
            <ion-item slot="header">
              <ion-label>Disponibilidad del sistema (%)</ion-label>
            </ion-item>
            <div class="ion-padding" slot="content">Porcentaje de tiempo mensual en que la app está operativa sin caídas.</div>
          </ion-accordion>
          <ion-accordion value="3">
            <ion-item slot="header">
              <ion-label>Porcentaje de errores de escaneo</ion-label>
            </ion-item>
            <div class="ion-padding" slot="content">Proporción de escaneos incorrectos en relación al total de cartas escaneadas.</div>
          </ion-accordion>
          <ion-accordion value="4">
            <ion-item slot="header">
              <ion-label>Tiempo de carga de noticias</ion-label>
            </ion-item>
            <div class="ion-padding" slot="content">Tiempo medio (en segundos) que tarda en cargar la sección de noticias.</div>
          </ion-accordion>
          <ion-accordion value="5">
            <ion-item slot="header">
              <ion-label>Copias de seguridad completadas</ion-label>
            </ion-item>
            <div class="ion-padding" slot="content">Porcentaje de días del mes en que se ha generado correctamente una copia de seguridad.</div>
          </ion-accordion>
        </ion-accordion-group>
      </ion-content>
    </ion-page>
  </template>
  
  
  <script setup lang="ts">
  import { IonButtons, IonContent, IonHeader, IonMenuButton, IonPage, IonTitle, IonToolbar, IonAccordionGroup, IonAccordion, IonItem, IonLabel } from '@ionic/vue';
  </script>
  
  
  <style scoped>
  
  
  ion-accordion.accordion-collapsing ion-item[slot='header'],
    ion-accordion.accordion-collapsed ion-item[slot='header'] {
      --background: var(--ion-color-light);
      --color: var(--ion-color-light-contrast);
    }
  
  
    ion-accordion.accordion-expanding ion-item[slot='header'],
    ion-accordion.accordion-expanded ion-item[slot='header'] {
      --background: rgba(var(--ion-color-primary-rgb), 0.14);
      color: var(--ion-color-primary);
    }
  
  
  </style>
  