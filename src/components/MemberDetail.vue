<template>
  <ion-header>
    <ion-toolbar>
      <ion-title>Member Details</ion-title>
      <ion-buttons slot="end">
        <ion-button @click="dialogDismiss()">
          <ion-icon :icon="close"></ion-icon></ion-button>
      </ion-buttons>
    </ion-toolbar>
  </ion-header>
  <ion-content class="ion-padding">
    <ion-list>
      <ion-item v-if="member.isVerified == true">
        <ion-icon :icon="checkmarkCircle" slot="start"></ion-icon>
        <ion-label>Verified</ion-label>
      </ion-item>
      <ion-item v-else>
        <ion-icon :icon="alertCircle" slot="start" color="warning"></ion-icon>
        <ion-label>Not Verified</ion-label>
      </ion-item>
      <ion-item>
        <ion-icon :icon="person" slot="start"></ion-icon>
        <ion-label>
          {{ member.title + ' ' + member.fName + ' ' + member.lName }}
        </ion-label>
      </ion-item>
      <ion-item v-if="member.email">
        <ion-icon aria-hidden="true" :icon="mail" slot="start"></ion-icon>
        <ion-label>{{ member.email }}</ion-label>
      </ion-item>
      <ion-item v-if="member.phoneNum">
        <ion-icon :icon="call" slot="start"></ion-icon>
        <ion-label>{{ member.phoneNum }}</ion-label>
      </ion-item>
      <ion-item v-if="member.birthday">
        <ion-icon src="/icons/CakeRound.svg" slot="start"> </ion-icon>
        <ion-label>{{ birthday }}</ion-label>
      </ion-item>
    </ion-list>
  </ion-content>
  <ion-footer>
    <ion-note color="medium">Member ID: {{ member.id }}</ion-note><br />
    <ion-note color="medium">Date Added: {{ dateAdded }} - Latest Edit Date:
      {{ dateEdited }}</ion-note>
  </ion-footer>
</template>

<script setup lang="ts">
import { MemberInterface } from '@/interface/MemberInterface'
import {
  IonHeader,
  IonToolbar,
  IonTitle,
  IonContent,
  IonList,
  IonItem,
  IonLabel,
  IonButtons,
  IonButton,
  IonIcon,
  IonFooter,
  IonNote,
  modalController,
} from '@ionic/vue'

import {
  close,
  mail,
  person,
  call,
  checkmarkCircle,
  alertCircle,
} from 'ionicons/icons'
const { member } = defineProps<{ member: MemberInterface }>()
const dialogDismiss = () => modalController.dismiss()

const dateAdded = member.dateAdded
  ? new Date(member.dateAdded).toLocaleString()
  : 'Not Found'
const dateEdited = member.dateEdited
  ? new Date(member.dateEdited).toLocaleString()
  : 'Not Found'
const birthday = member.birthday
  ? new Date(member.birthday).toDateString()
  : 'Not Found'
</script>
<style scoped>
ion-icon {
  color: var(--ion-color-primary);
}

ion-footer {
  padding: 0.5rem;
}
</style>
