<template>
  <ion-header>
    <ion-toolbar>
      <ion-title>Editing Member</ion-title>
      <ion-buttons slot="end">
        <ion-button @click="dialogDismiss()">
          <ion-icon :icon="close"></ion-icon></ion-button>
      </ion-buttons>
    </ion-toolbar>
  </ion-header>
  <ion-content class="ion-padding">
    <ion-item>
      <ion-select label="Name Title" v-model="editedMember.title" cancel-text="Cancel" ok-text="Save"
        justify="space-between" :interface-options="nameTitleOptions" label-placement="floating">
        <ion-select-option value="Mr." aria-selected="true">Mr.</ion-select-option>
        <ion-select-option value="Mrs.">Mrs.</ion-select-option>
        <ion-select-option value="Ms.">Ms.</ion-select-option>
      </ion-select>
    </ion-item>
    <ion-item>
      <ion-input label="First Name" type="text" v-model="editedMember.fName" placeholder="Enter Your First Name"
        label-placement="floating"></ion-input>
    </ion-item>
    <ion-item>
      <ion-input label="Last Name" type="text" v-model="editedMember.lName" placeholder="Enter Your Last Name"
        label-placement="floating"></ion-input>
    </ion-item>
    <ion-item>
      <ion-label>Birthday</ion-label>
      <ion-datetime-button datetime="datetime"></ion-datetime-button>
    </ion-item>
    <ion-item>
      <ion-input label="E-Mail" type="email" v-model="editedMember.email" placeholder="Enter Your E-Mail"
        label-placement="floating"></ion-input>
    </ion-item>
    <ion-item>
      <ion-input label="Phone Number" type="tel" v-model="editedMember.phoneNum" placeholder="Enter Your Phone Number"
        label-placement="floating"></ion-input>
    </ion-item>
    <ion-item>
      <ion-checkbox v-model="editedMember.isVerified">Verified?</ion-checkbox>
    </ion-item>
    <ion-button expand="block" class="ion-margin-top" @click="updateData()"
      :disabled="isMemberUnchanged">Save</ion-button>

    <ion-modal :keep-contents-mounted="true">
      <ion-datetime v-model="editedMember.birthday" id="datetime" presentation="date" :first-day-of-week="1"
        :max="dateToday">
        <span slot="title">Select your Birthday</span></ion-datetime>
    </ion-modal>
  </ion-content>
</template>
<script setup lang="ts">
import { updateMember } from '@/services/FirebaseService'
import { MemberInterface } from '@/interface/MemberInterface'
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
  IonSelectOption,
  IonCheckbox,
  IonModal,
  IonLabel,
  IonDatetime,
  IonDatetimeButton,
  modalController,
} from '@ionic/vue'
import { Timestamp } from 'firebase/firestore'
import { close } from 'ionicons/icons'
import { computed, ref } from 'vue'

const memberProp = defineProps<{
  member: MemberInterface
}>()
const dateToday = Timestamp.now().toDate().toISOString()
const editedMember = ref({ ...memberProp.member })
const oldMember = ref({ ...editedMember.value })

defineExpose({ editedMember })

const isMemberUnchanged = computed(() => {
  return JSON.stringify(editedMember.value) === JSON.stringify(oldMember.value)
})

const nameTitleOptions = {
  header: 'Your Name Title',
  subHeader: 'Please Select One',
}

const updateData = async () => {
  dialogDismiss()
  editedMember.value.dateEdited = Timestamp.now().toMillis()
  try {
    await updateMember(memberProp.member.id, editedMember.value)
      .then(async (message: string) => {
        await memberToast(message)
      })
      .catch(async (error: string) => {
        await memberToast(error)
      })
  } catch (e: any) {
    console.error(e.message)
  }
}

const dialogDismiss = () => modalController.dismiss()
</script>
