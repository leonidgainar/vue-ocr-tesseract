<template>
  <v-app>
    <v-main>
      <v-container class="mt-5">
        <v-row justify="center">
          <v-col cols="11" md="6">
            <h1 class="text-center py-5">Vue OCR with Tesseract.js</h1>
            <FileUploadComponent
              @start-loading-image="onStartLoadingImage"
              @finish-loading-image="onFinishLoadingImage"
              @finish-text-recognition="onFinishTextRecognition"
            />
          </v-col>
        </v-row>
        <v-row class="mt-5" justify="center">
          <v-col cols="11" sm="6">
            <h3 class="text-center">Loaded image:</h3>
            <v-img contain :src="loadedImage" />
          </v-col>
          <v-col cols="11" sm="6">
            <h3 class="text-center">Recognized text:</h3>
            <v-textarea
              name="recognized-text"
              label="Text from loaded image"
              filled
              auto-grow
              v-model="recognizedText"
            ></v-textarea>
          </v-col>
        </v-row>
      </v-container>
    </v-main>
  </v-app>
</template>

<script>
import FileUploadComponent from "./components/FileUploadComponent.vue";
export default {
  name: "App",
  components: {
    FileUploadComponent
  },

  data: () => ({
    recognizedText: "",
    loadedImage: null
  }),

  methods: {
    onStartLoadingImage() {
      this.recognizedText = "";
      this.loadedImage = null;
    },

    onFinishLoadingImage(image) {
      this.loadedImage = image;
    },

    onFinishTextRecognition(text) {
      this.recognizedText = text;
    }
  }
};
</script>
