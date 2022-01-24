<template>
  <v-row class="text-center">
    <v-col cols="12">
      <h2 class="text-left">Select language:</h2>
      <v-radio-group v-model="currentLanguage">
        <v-radio
          v-for="language in languages"
          :key="language.value"
          :label="language.label"
          :value="language.value"
          color="indigo"
        ></v-radio>
      </v-radio-group>
      <v-file-input
        :rules="rules"
        accept="image/png, image/jpeg, image/bmp"
        label="Upload image with text"
        show-size
        v-model="file"
        persistent-clear
      ></v-file-input>
    </v-col>
    <v-col justify="center">
      <v-btn color="primary" :disabled="!file" @click="uploadImage">
        Process image
      </v-btn>
    </v-col>
  </v-row>
  <FileLoaderDialogComponent :loading="isLoading" />
</template>

<script>
import FileLoaderDialogComponent from "./FileLoaderDialogComponent.vue";
import { createWorker } from "tesseract.js";

export default {
  components: {
    FileLoaderDialogComponent
  },

  data: () => ({
    file: null,
    isLoading: false,
    rules: [
      (value) =>
        !value || value.size < 2000000 || "Image size should be less than 2 MB!"
    ],
    languages: [
      { label: "English", value: "eng" },
      { label: "Romanian", value: "ron" },
      { label: "Russian", value: "rus" },
      { label: "French", value: "fra" }
    ],
    currentLanguage: "eng"
  }),

  methods: {
    uploadImage() {
      this.$emit("start-loading-image");
      let image = null;

      var reader = new FileReader();

      reader.readAsDataURL(this.file[0]);

      reader.onload = function (e) {
        image = e.target.result;
      };

      const _this = this;
      reader.onloadend = function () {
        if (reader.readyState === 2) {
          _this.recognizeTextFromImage(image);
        }
      };
    },

    async recognizeTextFromImage(image) {
      this.isLoading = true;

      const worker = createWorker();

      await worker.load();
      await worker.loadLanguage(this.currentLanguage);
      await worker.initialize(this.currentLanguage);
      const {
        data: { text }
      } = await worker.recognize(image);
      this.$emit("finish-text-recognition", text);
      this.$emit("finish-loading-image", image);
      await worker.terminate();

      this.isLoading = false;
      this.file = null;
    }
  }
};
</script>
