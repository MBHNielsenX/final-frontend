<template>
  <q-page padding>
    <div class="q-pa-md flex flex-center">
      <q-card class="q-ma-md" style="max-width: 400px; width: 100%;">
        <q-card-section>
          <div class="text-h6 text-center">Upload Excel File</div>
        </q-card-section>

        <q-card-section>
          <div class="q-mb-md">
            <q-btn
              class="bg-secondary text-accent"
              label="Select File"
              @click="triggerFileInput"
              style="width: 100%;"
            />
            <input
              ref="fileInput"
              type="file"
              @change="handleFileUpload"
              accept=".xlsx, .xls"
              style="display: none;"
            />
            <div v-if="selectedFile" class="q-mt-md text-center">{{ selectedFile.name }}</div>
          </div>
        </q-card-section>

        <q-card-actions align="center">
          <q-btn
            class="bg-secondary text-accent q-mt-md"
            @click="uploadFile"
            :disable="!selectedFile"
          >
            Upload
          </q-btn>
        </q-card-actions>
      </q-card>
    </div>
  </q-page>
</template>

<script>
import axios from 'axios'
import { defineComponent } from 'vue'

export default defineComponent({
  name: 'ExcelReaderPage',
  data() {
    return {
      selectedFile: null,
    }
  },
  methods: {
    triggerFileInput() {
      this.$refs.fileInput.click()
    },
    handleFileUpload(event) {
      this.selectedFile = event.target.files[0]
    },
    async uploadFile() {
      if (this.selectedFile) {
        const formData = new FormData()
        formData.append('file', this.selectedFile)

        try {
          const response = await axios.post('https://warehouse-schedule.azurewebsites.net/api/excel/upload', formData, {
            headers: {
              'Content-Type': 'multipart/form-data'
            }
          })
          console.log('File uploaded successfully', response.data)
          this.$q.notify({
            type: 'positive',
            message: 'File uploaded successfully!'
          })
        } catch (error) {
          console.error('Error uploading file', error)
          this.$q.notify({
            type: 'negative',
            message: 'Failed to upload file!'
          })
        }
      }
    }
  }
})
</script>

<style scoped>
.q-pa-md {
  max-width: 600px;
  margin: auto;
}

.q-card {
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.2);
  border-radius: 8px;
}

.q-btn {
  width: 100%;
}

.q-card-section {
  padding: 16px;
}
</style>
