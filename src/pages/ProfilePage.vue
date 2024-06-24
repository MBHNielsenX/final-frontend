<template>
  <q-page class="flex flex-center">
    <div>
      <span class="text-h4">
        Profile
      </span>
    </div>
  </q-page>
</template>

<script>
import { useRouter } from "vue-router";

export default {
  name: 'ProfilePage',

  data() {
    return {
      router: useRouter()
    }
  }
}
</script>


