<template>
  <v-container>
    <h1>{{ isEdit ? "Update user" : "Add User" }}</h1>
    <v-form
      ref="form"
      v-model="isFormValid"
    >
      <v-text-field
        v-model="name"
        :rules="[rules.required]"
        label="Name"
        required 
      />

      <v-select
        v-model="gender"
        :items="genderList"
        label="Gender"
        :rules="[rules.required]"
        required
      />

      <v-menu>
        <template v-slot:activator="{ on, attrs }">
          <v-text-field
            v-model="dob"
            :rules="[rules.required, rules.dob]"
            label="Date of birth"
            prepend-icon="mdi-calendar"
            readonly
            v-bind="attrs"
            v-on="on"
            required
          ></v-text-field>
        </template>
        <v-date-picker 
          v-model="dob" 
          no-title 
          scrollable
          :max="today"
        >
          <v-spacer></v-spacer>
          <v-btn
            text
            color="primary"
            @click="menu = false"
          >
            Cancel
          </v-btn>
          <v-btn
            text
            color="primary"
            @click="$refs.menu.save(dob)"
          >
            OK
          </v-btn>
        </v-date-picker>
      </v-menu>

      <v-text-field
        v-model="address"
        :rules="[rules.required]"
        label="Address"
        required
      />

      <v-text-field
        v-model="email"
        :rules="[rules.required, rules.email]"
        label="Email"
        required
      />

      <v-select
        v-model="userRoleId"
        :items="userRoles"
        item-text="name"
        item-value="id"
        label="Role"
        :rules="[rules.required]"
        required
      />

      <v-btn
        color="success"
        class="mr-4"
        @click="submit"
        :disabled="!isFormValid"
      >
        {{ isEdit ? 'Update' : 'Submit' }}
      </v-btn>
    </v-form>
  </v-container>
</template>

<script>
import axios from 'axios';
import { API_URL } from '../utils/const'

export default {
  name: 'UserForm',
  props: {
    user: {
      type: Object,
      default: () => ({})
    },
    isEdit: {
      type: Boolean,
      default: false
    }
  },
  data: () => ({
    name: '',
    gender: '',
    dob: '',
    address: '',
    email: '',
    userRoleId: '',
    userRoles: [],
    genderList: [
      {text: "Male", value: "MALE"},
      {text: "Female", value: "FEMALE"}
    ],
    isFormValid: false,
    rules: {
      required: value => !!value || 'Required',
      email: value => /.+@.+\..+/.test(value) || 'E-mail must be valid',
      dob: value => {
        const today = new Date()
        const dob = new Date(value)
        return dob < today || 'Date of birth must be less than today'
      },
    },
    today: new Date().toISOString().substr(0, 10),
  }),
  mounted() {
    this.getUserRoles()
    console.log(this.user)
    if (this.isEdit) {
      this.name = this.user.name
      this.gender = this.user.gender
      this.dob = this.user.dob
      this.address = this.user.address
      this.email = this.user.email
      this.userRoleId = this.user.userRole.id
    }
  },
  methods: {
    submit() {
      const payload = {
        name: this.name,
        gender: this.gender,
        dob: this.dob,
        address: this.address,
        email: this.email,
        userRoleId: this.userRoleId
      }
      
      if (this.isEdit) {
        axios.put(`${API_URL}/user/${this.user.id}`, payload)
          .then(() => {
            this.clearInput()
            this.$emit('success')
          })
          .catch(() => {
            this.$emit('error')
          }
        )
        return
      }

      axios.post(`${API_URL}/user/`, payload)
        .then(() => {
          this.clearInput()
          this.$emit('success')
        })
        .catch(() => {
          this.$emit('error')
        }
      )
    },
    getUserRoles() {
      axios.get(`${API_URL}/user-role/?name=`)
        .then(res => {
          this.userRoles = res.data
        })
        .catch(err => {
          console.log(err)
        })
    },
    clearInput() {
      this.name = '',
      this.gender = '',
      this.dob = '',
      this.address = '',
      this.email = '',
      this.userRoleId = ''
      this.$refs.form.reset()
    }
  }
  
}
</script>