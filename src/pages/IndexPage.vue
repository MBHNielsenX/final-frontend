<template>
  <q-page class="flex flex-center">
    <section class="desktop desktop-background q-pa-xl">
      <div class="text-center q-mb-xl">
        <q-img
          src="src/assets/Lidl.png"
          contain
          style="width: 150px; height: 150px; border-radius: 50%;"
        />
      </div>
      <div class="text-center q-mb-xl">
        <span class="text-h2 text-primary">Welcome to Lidl Warehouse Schedule</span>
        <div class="text-subtitle2 q-mt-md">
          A warehouse schedule application to eliminate the need for paper schedules.
        </div>
      </div>
      <div class="text-center q-mt-lg">
        <q-btn
          class="q-px-xl"
          push
          size="lg"
          color="primary"
          text-color="white"
          icon="login"
          label="Get Started"
          @click="getStarted"
        />
      </div>
    </section>

  </q-page>

</template>

<script>
import { useRouter } from 'vue-router'

export default {
  name: 'IndexPage',
  setup() {
    const router = useRouter()

    const getStarted = () => {
      router.push('/ShiftPage')
    }

    return {
      getStarted
    }
  }
}
</script>

<style scoped>
.desktop-background {
  background: linear-gradient(to right, #f5f7fa, #c3cfe2);
  border-radius: 15px;
  padding: 30px;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.2);
}
</style>
