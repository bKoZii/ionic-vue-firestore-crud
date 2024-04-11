<template>
  <ion-page>
    <ion-header :translucent="true">
      <ion-toolbar>
        <ion-title>Vue 3 + Ionic 7 - Firestore CRUD</ion-title>
      </ion-toolbar>
    </ion-header>
    <ion-content :fullscreen="true">
      <ion-fab slot="fixed" vertical="top" horizontal="end" :edge="true">
        <ion-fab-button @click="openModal(AddMemberModal)">
          <ion-icon :icon="personAdd"></ion-icon>
        </ion-fab-button>
      </ion-fab>
      <ion-grid :fixed="true">
        <ion-row class="ion-justify-content-center">
          <ion-col size="12" size-lg="6" v-if="!isCollectionEmpty">
            <div v-for="member in memberData" :key="member.id">
              <MemberList :data="member" />
            </div>
          </ion-col>
          <ion-col v-else>
            <ion-label>No Data Yet, Try Adding Some Now!</ion-label>
          </ion-col>
        </ion-row>
      </ion-grid>
    </ion-content>
  </ion-page>
</template>

<script setup lang="ts">
import {
  IonContent,
  IonHeader,
  IonPage,
  IonTitle,
  IonToolbar,
  IonFab,
  IonFabButton,
  IonIcon,
  IonGrid,
  IonCol,
  IonRow,
  IonLabel,
} from '@ionic/vue'
import { personAdd } from 'ionicons/icons'
import { computed, defineAsyncComponent } from 'vue'
import { openModal } from '../services/UtilService'
import { useCollection } from 'vuefire'
import { MemberInterface } from '@/interface/MemberInterface'
import { memberRef } from '@/services/FirebaseService'
const MemberList = defineAsyncComponent(
  () => import('@/components/MemberList.vue')
)
const AddMemberModal = defineAsyncComponent(
  () => import('@/components/AddMember.vue')
)

const memberData = useCollection<MemberInterface>(memberRef)

const isCollectionEmpty = computed(() => {
  return memberData.value.length === 0
})
</script>
