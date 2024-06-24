<template>
  <q-dialog v-model="notificationDialog" seamless position="bottom">
    <q-card :class="[notificationClass, 'q-px-md', 'q-pt-md', 'q-pb-md']" style="width: 100vw">
      <div class="text-body2 text-white">{{ notificationMessage }}</div>
    </q-card>
  </q-dialog>
</template>

<script>
import { ref } from "vue"

export default {
  name: "notificationBanner",
  setup () {
    const notificationDialog = ref(false)
    const notificationMessage = ref("")
    const notificationClass = ref('bg-red')

    const displayNotification = (message, type) => {
      switch (type.toLowerCase()){
        case 'error': notificationClass.value = 'bg-red'
          break
        case 'success': notificationClass.value = 'bg-green'
          break
        case 'notification': notificationClass.value = 'bg-blue'
      }
      notificationMessage.value = message
      notificationDialog.value = true
      setTimeout(() => {
        notificationDialog.value = false
      }, 3000);
    }

    return{
      notificationDialog,
      notificationMessage,
      notificationClass,
      displayNotification
    }
  }
}
</script>
