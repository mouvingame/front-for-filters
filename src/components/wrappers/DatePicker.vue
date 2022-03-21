<template>
  <v-menu
      v-model="show"
      :close-on-content-click="false"
      offset-y
  >
    <template v-slot:activator="{ on, attrs }">
      <v-text-field
          v-model="date"
          prepend-inner-icon="mdi-calendar"
          readonly
          outlined
          dense
          v-bind="attrs"
          v-on="on"
          :rules="[val => !!val || 'This field is required']"
      ></v-text-field>
    </template>
    <v-date-picker
        v-model="date"
        @input="show = false; $emit('value-change', $event)"
        no-title
    ></v-date-picker>
  </v-menu>
</template>

<script>
export default {
  data() {
    return {
      show: false,
      date: '',
    }
  },
}
</script>

<style scoped>

</style>