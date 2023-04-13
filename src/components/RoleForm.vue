<template>
  <v-container>
    <v-row>
      <v-col cols="12">
        <v-card>
          <v-card-title>
            <h2 class="headline mb-0">Role</h2>
          </v-card-title>
          <v-card-text>
            <v-form ref="form" v-model="valid">
              <v-text-field
                v-model="name"
                :rules="nameRules"
                label="Name"
                required
              ></v-text-field>
            </v-form>
          </v-card-text>
          <v-card-actions>
            <v-spacer></v-spacer>
            <v-btn color="error" text @click="close()">Cancel</v-btn>
            <v-btn :disabled="!valid" color="success" text @click="submit">Submit</v-btn>
          </v-card-actions>
        </v-card>
      </v-col>
    </v-row>
  </v-container>
</template>

<script>
import axios from 'axios';
import { API_URL } from '../utils/const'
export default {
  name: 'RoleForm',

  data() {
    return {
      name: '',
      valid: true,
      nameRules: [(v) => !!v || 'Name is required'],
    };
  },

  methods: {
    close() {
      this.$emit('close');
    },
    submit() {
      if (this.$refs.form.validate()) {
        axios.post(`${API_URL}/user-role/`, {
          name: this.name,
        })
          .then(() => {
            console.log("success");
            this.$emit('success');
          })
          .catch(() => {
            this.$emit('error');
          });
      }
    },
  },
}
</script>