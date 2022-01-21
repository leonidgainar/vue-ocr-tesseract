<template>
  <v-dialog v-model="isLoading" hide-overlay persistent>
    <v-card color="primary" dark>
      <v-card-text>
        Please wait, file is processed...
        <v-progress-linear
          indeterminate
          color="white"
          class="mb-0"
        ></v-progress-linear>
      </v-card-text>
    </v-card>
  </v-dialog>
</template>

<script>
export default {
  props: {
    loading: {
      type: Boolean,
      default: false
    }
  },

  data() {
    return {
      isLoading: false
    };
  },

  watch: {
    loading() {
      this.isLoading = this.loading;
    }
  }
};
</script>
