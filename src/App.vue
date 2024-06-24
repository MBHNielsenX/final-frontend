<template>
  <router-view />
</template>

<script>
import { defineComponent } from 'vue'

export default defineComponent({
  name: 'App',
  metaInfo: {
    title: 'WarehouseSchedule',
    titleTemplate: '%s | WarehouseSchedule'
  }
})
</script>
