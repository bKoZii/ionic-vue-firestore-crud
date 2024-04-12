<template>
  <ion-header>
    <ion-toolbar>
      <ion-title>Add Member</ion-title>
      <ion-buttons slot="end">
        <ion-button @click="dialogDismiss()">
          <ion-icon :icon="close"></ion-icon></ion-button>
      </ion-buttons>
    </ion-toolbar>
  </ion-header>
  <ion-content class="ion-padding">
    <ion-item>
      <ion-select label="Name Title" v-model="member.title" cancel-text="Cancel" ok-text="Save" justify="space-between"
        :interface-options="nameTitleOptions" label-placement="floating">
        <ion-select-option value="Mr." aria-selected="true">Mr.</ion-select-option>
        <ion-select-option value="Mrs.">Mrs.</ion-select-option>
        <ion-select-option value="Ms.">Ms.</ion-select-option>
      </ion-select>
    </ion-item>
    <ion-item>
      <ion-input label="First Name" type="text" v-model="member.fName" placeholder="Enter Your First Name"
        label-placement="floating"></ion-input>
    </ion-item>
    <ion-item>
      <ion-input label="Last Name" type="text" v-model="member.lName" placeholder="Enter Your Last Name"
        label-placement="floating"></ion-input>
    </ion-item>
    <ion-item>
      <ion-label>Birthday</ion-label>
      <ion-datetime-button datetime="datetime"></ion-datetime-button>
    </ion-item>
    <ion-item>
      <ion-input label="E-Mail" type="email" v-model="member.email" placeholder="Enter Your E-Mail"
        label-placement="floating"></ion-input>
    </ion-item>
    <ion-item>
      <ion-input label="Phone Number" type="tel" v-model="member.phoneNum" placeholder="Enter Your Phone Number"
        label-placement="floating"></ion-input>
    </ion-item>
    <ion-button expand="block" class="ion-margin-top" @click="sendData()"
      :disabled="!member.fName || !member.lName">Save</ion-button>
    <ion-button expand="block" :disabled="isMemberDataEmpty" fill="clear" @click="clearMember()">Reset Form</ion-button>
    <ion-modal :keep-contents-mounted="true">
      <ion-datetime v-model="member.birthday" id="datetime" presentation="date" :first-day-of-week="1" :max="dateToday">
        <span slot="title">Select your Birthday</span></ion-datetime>
    </ion-modal>
  </ion-content>
</template>
<script setup lang="ts">
import { addMember } from '@/services/FirebaseService'
import { memberToast } from '@/services/UtilService'
import {
  IonButton,
  IonButtons,
  IonIcon,
  IonHeader,
  IonToolbar,
  IonTitle,
  IonContent,
  IonItem,
  IonInput,
  IonSelect,
  IonModal,
  IonDatetime,
  IonDatetimeButton,
  IonSelectOption,
  IonLabel,
  modalController,
} from '@ionic/vue'
import { close } from 'ionicons/icons'
import { computed, ref } from 'vue'
import { Timestamp } from 'firebase/firestore'
import { MemberInterface } from '@/interface/MemberInterface'

const member = ref<MemberInterface>({} as unknown as MemberInterface)
const dateToday = Timestamp.now().toDate().toISOString()
const nameTitleOptions = {
  header: 'Your Name Title',
  subHeader: 'Please Select One',
}

const sendData = async () => {
  member.value.dateAdded = Timestamp.now().toMillis()
  member.value.isVerified = false
  dialogDismiss()

  try {
    await addMember({ ...member.value })
      .then(async (message: string) => {
        await memberToast(message)
      })
      .catch(async (error: string) => {
        await memberToast('ERROR: ' + error)
      })
  } catch (e: any) {
    console.error('Error Adding Member: ', e)
    await memberToast('ERROR: ' + e.message)
  }
}

const dialogDismiss = () => modalController.dismiss()

const clearMember = () => {
  const clearedMember = {
    ...member.value,
    ...Object.keys(member.value).reduce((acc, key) => {
      if (key === 'birthday') {
        return { ...acc, [key]: dateToday }
      } else {
        return { ...acc, [key]: '' }
      }
    }, {}),
  }
  member.value = clearedMember
}

const isMemberDataEmpty = computed(() => {
  return Object.values(member.value).every((value: any) => {
    typeof value === 'string' ? value.trim() === '' : true
  })
})
</script>
