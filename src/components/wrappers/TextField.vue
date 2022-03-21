<template>
  <v-text-field
      outlined
      dense
      value=""
      :type="thatNumber ? 'number' : 'text'"
      :rules="[val => val.trim().length > 0 || 'This field is required']"
      @input="$emit('value-change', $event.trim())"
  ></v-text-field>
</template>

<script>
export default {
  props: ['thatNumber'],
}
</script>

<style scoped>

</style>