<template>
  <v-app>
    <v-main>
      <todolist/>
    </v-main>
  </v-app>
</template>

<script>
import todolist from './components/todolist.vue'

export default {
  name: 'App',

  components: {
    todolist,
  },

  data: () => ({
    //
  }),
}
</script>
