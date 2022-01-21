<template>
  <v-row class="text-center">
    <v-col cols="12">
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
    ]
  }),

  methods: {
    uploadImage() {
      this.$emit("start-loading-image");
      let image = null;
      const _this = this;
      var reader = new FileReader();

      reader.readAsDataURL(this.file[0]);

      reader.onload = function (e) {
        image = e.target.result;
      };

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
      await worker.loadLanguage("eng");
      await worker.initialize("eng");
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
