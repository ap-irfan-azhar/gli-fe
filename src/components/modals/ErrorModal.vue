<template>
  <v-container>
    <v-row>
      <v-col cols="12">
        <v-card>
          <v-card-title>
            <h2 class="headline mb-0">Error</h2>
          </v-card-title>
          <v-card-text>
            <p>{{ message }}</p>
          </v-card-text>
          <v-card-actions>
            <v-spacer></v-spacer>
            <v-btn color="error" text @click="close">OK</v-btn>
          </v-card-actions>
        </v-card>
      </v-col>
    </v-row>
  </v-container>
</template>

<script>

export default {
  name: 'ErrorModal',

  props: {
    message: {
      type: String,
      required: true,
    },
  },

  methods: {
    close() {
      this.$emit('close');
    },
  },
};
</script>