<template>
  <v-row>
    <v-col cols="12">
      <v-card>
        <v-card-title>
          <h2 class="headline mb-0">Confirmation</h2>
        </v-card-title>
        <v-card-text>
          <p>{{ message }}</p>
        </v-card-text>
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn color="error" text @click="close(false)">Cancel</v-btn>
          <v-btn color="success" text @click="close(true)">OK</v-btn>
        </v-card-actions>
      </v-card>
    </v-col>
  </v-row>
</template>

<script>

export default {
  name: 'ConfirmationModal',

  props: {
    message: {
      type: String,
      required: true,
    },
  },

  methods: {
    close(value) {
      if (value) {
        this.$emit('confirm');
      } else {
        this.$emit('cancel');
      }
    },
  },
};
</script>