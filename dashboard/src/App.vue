<template>
  <ion-app>
    <ion-split-pane content-id="main-content">
      <ion-menu content-id="main-content" type="overlay" class="dark-menu">
        <ion-content>
          <ion-list id="inbox-list">
            <ion-list-header>Mana Market</ion-list-header>
            <ion-note>Data Visualization</ion-note>

            <ion-menu-toggle :auto-hide="false" v-for="(p, i) in appPages" :key="i">
              <ion-item @click="selectedIndex = i" router-direction="root" :router-link="p.url" lines="none" :detail="false" class="hydrated" :class="{ selected: selectedIndex === i }">
                <ion-icon aria-hidden="true" slot="start" :ios="p.iosIcon" :md="p.mdIcon"></ion-icon>
                <ion-label>{{ p.title }}</ion-label>
              </ion-item>
            </ion-menu-toggle>
          </ion-list>
        </ion-content>
      </ion-menu>
      <ion-router-outlet id="main-content"></ion-router-outlet>
    </ion-split-pane>
  </ion-app>
</template>

<script setup lang="ts">
import { IonApp, IonContent, IonIcon, IonItem, IonLabel, IonList, IonListHeader, IonMenu, IonMenuToggle, IonNote, IonRouterOutlet, IonSplitPane,} from '@ionic/vue';
import { rocketOutline, rocketSharp, pulseOutline, pulseSharp, speedometerOutline, speedometerSharp } from 'ionicons/icons';
import { ref, onMounted, watch } from 'vue';
import { useRoute } from 'vue-router';

const selectedIndex = ref(0);
const appPages = [
  {
    title: 'Negocio',
    url: '/negocio',
    iosIcon: rocketOutline,
    mdIcon: rocketSharp,
  },
  {
    title: 'Técnico',
    url: '/tecnico',
    iosIcon: pulseOutline,
    mdIcon: pulseSharp,
  },
  {
    title: 'KPIs',
    url: '/kpis',
    iosIcon: speedometerOutline,
    mdIcon: speedometerSharp,
  },
];

const route = useRoute();

/********************************************************************** */
// 🔄 Función para actualizar el `selectedIndex` según la URL actual

const updateSelectedIndex = () => {
  const currentPath = route.path;
  const index = appPages.findIndex(page => page.url === currentPath);
  if (index !== -1) {
    selectedIndex.value = index;
  }
};

// Ejecutar cuando la app carga
onMounted(updateSelectedIndex);

// Ejecutar cada vez que cambia la ruta
watch(route, updateSelectedIndex);
/********************************************************************** */

</script>

<style scoped>
ion-split-pane {
  --side-max-width: 280px;
}

/* Estilos para el tema oscuro */
.dark-menu {
  --ion-background-color: #000000;
  --ion-text-color: #ffffff;
}

ion-menu ion-content {
  --background: #000000;
}

ion-menu ion-list {
  background: #000000;
}

ion-menu ion-item {
  --background: transparent;
  --color: #cccccc;
}

ion-menu.md ion-content {
  --padding-start: 8px;
  --padding-end: 8px;
  --padding-top: 20px;
  --padding-bottom: 20px;
}

ion-menu.md ion-list {
  padding: 20px 0;
}

ion-menu.md ion-note {
  margin-bottom: 30px;
  color: #aaaaaa;
}

ion-menu.md ion-list-header,
ion-menu.md ion-note {
  padding-left: 10px;
}

ion-menu.md ion-list#inbox-list {
  border-bottom: 1px solid rgba(255, 255, 255, 0.1);
}

ion-menu.md ion-list#inbox-list ion-list-header {
  font-size: 22px;
  font-weight: 600;
  min-height: 20px;
  color: #ffffff;
}

ion-menu.md ion-list#labels-list ion-list-header {
  font-size: 16px;
  margin-bottom: 18px;
  color: #aaaaaa;
  min-height: 26px;
}

ion-menu.md ion-item {
  --padding-start: 10px;
  --padding-end: 10px;
  border-radius: 4px;
}

ion-menu.md ion-item.selected {
  --background: rgba(240, 138, 36, 0.2);
}

ion-menu.md ion-item.selected ion-icon {
  color: #F08A24;
}

ion-menu.md ion-item ion-icon {
  color: #cccccc;
}

ion-menu.md ion-item ion-label {
  font-weight: 500;
}

ion-menu.ios ion-content {
  --padding-bottom: 20px;
}

ion-menu.ios ion-list {
  padding: 20px 0 0 0;
}

ion-menu.ios ion-note {
  line-height: 24px;
  margin-bottom: 20px;
  color: #aaaaaa;
}

ion-menu.ios ion-item {
  --padding-start: 16px;
  --padding-end: 16px;
  --min-height: 50px;
}

ion-menu.ios ion-item.selected ion-icon {
  color: #F08A24;
}

ion-menu.ios ion-item ion-icon {
  font-size: 24px;
  color: #cccccc;
}

ion-menu.ios ion-list#labels-list ion-list-header {
  margin-bottom: 8px;
}

ion-menu.ios ion-list-header,
ion-menu.ios ion-note {
  padding-left: 16px;
  padding-right: 16px;
}

ion-menu.ios ion-note {
  margin-bottom: 8px;
}

ion-note {
  display: inline-block;
  font-size: 16px;
  color: #aaaaaa;
}

ion-item.selected {
  --color: #F08A24;
}

/* Efecto hover para los elementos del menú */
ion-menu ion-item:hover {
  --background: rgba(255, 255, 255, 0.05);
}
</style>