<template>
  <v-row
      align="start"
  >
    <v-col
        cols="2"
    >
      <h3
          v-show="needLabel"
      >
        Criteria
      </h3>
    </v-col>
    <v-col
        cols="3"
    >
      <v-select
          :value="criterion.type"
          :items="types"
          @change="$emit('type-change', $event)"
          outlined
          dense
      ></v-select>
    </v-col>
    <v-col
        cols="3"
    >
      <v-select
          :value="criterion.condition"
          :items="conditions[criterion.type.toLowerCase()]"
          @change="$emit('condition-change', $event)"
          outlined
          dense
      ></v-select>
    </v-col>
    <v-col
        cols="3"
    >
      <component
          :is="criterion.valueComponent.name"
          :thatNumber="criterion.valueComponent.thatNumber"
          :key="`${criterion.valueComponent.name}${criterion.valueComponent.thatNumber}`"
          @value-change="$emit('value-change', $event)"
      ></component>
    </v-col>
    <v-col
        cols="1"
    >
      <v-btn
          depressed
          color="pink white--text"
          @click="$emit('remove')"
      >
        <v-icon>mdi-minus</v-icon>
      </v-btn>
    </v-col>
  </v-row>
</template>

<script>
import TextField from './wrappers/TextField.vue';
import DatePicker from './wrappers/DatePicker.vue';

export default {
  components: {
    TextField,
    DatePicker,
  },

  data() {
    return {
      types: [
        {text: 'Amount', value: 'AMOUNT'},
        {text: 'Title', value: 'TITLE'},
        {text: 'Date', value: 'DATE'},
      ],
      conditions: {
        amount: [
          {text: 'More', value: 'MORE'},
          {text: 'Less', value: 'LESS'},
        ],
        title: [
          {text: 'Starts with', value: 'STARTS_WITH'},
          {text: 'Ends with', value: 'ENDS_WITH'},
        ],
        date: [
          {text: 'From', value: 'FROM'},
          {text: 'Until', value: 'UNTIL'},
        ],
      },
    }
  },

  props: ['criterion', 'needLabel'],
}
</script>

<style scoped>

</style>