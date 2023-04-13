<template>
  <v-app>
    <v-main>
      <UsersPage />
    </v-main>
  </v-app>
</template>

<script>
import UsersPage from './components/UsersPage.vue';

export default {
  name: 'App',

  components: {
    UsersPage,
  },

  data: () => ({
    //
  }),
};
</script>
