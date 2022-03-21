<template>
  <v-card
      outlined
  >
    <v-card-title
        class="cyan lighten-1 white--text"
    >
      Filter
      <v-spacer></v-spacer>
      <v-btn
          icon
          tile
          color="white"
          @click="close"
      >
        <v-icon>mdi-window-close</v-icon>
      </v-btn>
    </v-card-title>
    <v-card-text
        class="pt-4"
        style="height: 460px; overflow: auto"
    >
      <v-row
          align="baseline"
      >
        <v-col
            cols="2"
        >
          <h3>
            Filter name
          </h3>
        </v-col>
        <v-col
            cols="3"
        >
          <v-text-field
              ref="name"
              v-model.trim="name"
              outlined
              dense
              :rules="[val => !!val.trim() || 'This field is required']"
          >
          </v-text-field>
        </v-col>
      </v-row>

      <template v-for="(criterion, index) in criteria">
        <NewCriterion
            :key="criterion.id"
            :criterion="criterion"
            :need-label="index === 0"
            @type-change="onTypeChange(criterion, $event)"
            @condition-change="criterion.condition = $event"
            @value-change="criterion.value = $event"
            @remove="criteria.splice(index, 1)"
        />
      </template>

      <v-row
          justify="center"
      >
        <v-col
            cols="auto"
        >
          <v-btn
              depressed
              color="grey white--text"
              @click="addCriterion"
          >
            <v-icon
                left
            >
              mdi-plus
            </v-icon>
            Add criterion
          </v-btn>
        </v-col>
      </v-row>
      <v-row
          align="baseline"
      >
        <v-col
            cols="2"
        >
          <h3>
            Selection
          </h3>
        </v-col>
        <v-col>
          <v-radio-group
              v-model="selection"
              mandatory
              row
          >
            <v-radio
                v-for="n in 3"
                :key="n"
                :label="`Select ${n}`"
                :value="n"
            ></v-radio>
          </v-radio-group>
        </v-col>
      </v-row>
    </v-card-text>
    <v-card-actions
        class="grey lighten-3 justify-center"
    >
      <v-col
          cols="auto"
      >
        <v-btn
            depressed
            color="grey white--text"
            @click="close"
        >
          Close
        </v-btn>
      </v-col>
      <v-col
          cols="auto"
      >
        <v-btn
            depressed
            color="primary white--text"
            :disabled="!formIsValid"
            :loading="loading"
            @click="save"
        >
          Save
        </v-btn>
      </v-col>
    </v-card-actions>
    <v-alert
        :value="alert"
        type="error"
        class="mb-0"
        dense
        tile
    >
      {{ alertText }}
    </v-alert>
  </v-card>
</template>

<script>
import NewCriterion from './NewCriterion.vue';

export default {
  data() {
    return {
      name: '',
      criterionId: 0,
      criteria: [],
      selection: 1,
      alert: false,
      alertText: '',
      loading: false,
    }
  },

  components: {
    NewCriterion,
  },

  computed: {
    formIsValid() {
      return (
          this.name &&
          this.criteria.length &&
          this.criteria.every(({type, condition, value}) => (type && condition && value)) &&
          this.selection
      )
    }
  },

  methods: {
    async save() {
      this.loading = true;

      if (this.alert) {
        this.alert = false;
        this.alertText = '';
      }

      let newFilter = {
        name: this.name,
        criteria: this.criteria.map(({type, condition, value}) => ({type, condition, value})),
        selection: this.selection
      };
      let response = await fetch('http://localhost:8080/api/filters', {
        method: 'POST',
        headers: {
          'Content-Type': 'application/json;charset=utf-8'
        },
        body: JSON.stringify(newFilter)
      });
      let json = await response.json();

      if (response.ok) {
        this.$emit('saved');
        this.close();
      } else {
        this.loading = false;
        this.alert = true;
        this.alertText = json.message;
      }
    },

    addCriterion() {
      this.criteria.push({
        id: ++this.criterionId,
        type: 'AMOUNT',
        condition: 'MORE',
        valueComponent: {
          name: 'TextField',
          thatNumber: true
        },
        value: '',
      });
    },

    onTypeChange(criterion, newType) {
      let initialCondition;
      let newValueComponentName;
      let isNumber;

      switch (newType) {
        case 'AMOUNT':
          initialCondition = 'MORE';
          newValueComponentName = 'TextField';
          isNumber = true;
          break;
        case 'TITLE':
          initialCondition = 'STARTS_WITH';
          newValueComponentName = 'TextField';
          isNumber = false;
          break;
        case 'DATE':
          initialCondition = 'FROM';
          newValueComponentName = 'DatePicker';
          break;
      }

      criterion.type = newType;
      criterion.condition = initialCondition;
      criterion.valueComponent.name = newValueComponentName;
      criterion.valueComponent.thatNumber = isNumber;
      criterion.value = '';
    },

    close() {
      this.name = '';
      this.$refs.name.resetValidation();
      this.criterionId = 0;
      this.criteria = [];
      this.selection = 1;
      this.alert = false;
      this.alertText = '';
      this.loading = false;
      this.$emit('close');
    }
  },
}
</script>

<style scoped>

</style>