<template>
  <v-app>
    <v-app-bar
        app
        color="primary"
        dark
    >
      <v-toolbar-title>
        Filters
      </v-toolbar-title>
      <v-spacer></v-spacer>
      <v-switch
          v-model="modalForm"
          v-show="showControls"
          label="Modal form"
          color="pink"
          hide-details
          inset
          class="mr-4"
      >
      </v-switch>
      <v-btn
          v-if="modalForm"
          v-show="showControls"
          depressed
          color="pink"
          @click.stop="showForm = true"
      >
        Add filter
      </v-btn>
      <v-btn
          v-else
          v-show="showControls"
          depressed
          color="pink"
          @click="showForm = true"
      >
        Add filter
      </v-btn>
    </v-app-bar>
    <v-main>
      <v-dialog
          v-if="modalForm"
          v-model="showForm"
          width="1100px"
          persistent
      >
        <CreateFilterForm
            @close="showForm = false"
            @saved="fetchFilters"
        />
      </v-dialog>
      <v-container
          v-else
          v-show="showForm"
      >
        <v-row
            justify="center"
        >
          <v-col
              cols="8"
          >
            <CreateFilterForm
                @close="showForm = false"
                @saved="fetchFilters"
            />
          </v-col>
        </v-row>
      </v-container>
      <v-simple-table>
        <thead>
        <tr>
          <th>
            Name
          </th>
          <th>
            Criteria
          </th>
          <th>
            Selection
          </th>
        </tr>
        </thead>
        <tbody>
        <tr
            v-for="filter in filters"
            :key="filter.id"
        >
          <td>
            {{ filter.name }}
          </td>
          <td>
            <ul>
              <li
                  v-for="(criterion, index) in filter.criteria"
                  :key="index"
              >
                {{ stringifyCriterion(criterion) }}
              </li>
            </ul>
          </td>
          <td>
            {{ filter.selection }}
          </td>
        </tr>
        </tbody>
      </v-simple-table>
    </v-main>
  </v-app>
</template>

<script>
import CreateFilterForm from './components/CreateFilterForm.vue';

export default {
  components: {
    CreateFilterForm,
  },

  data() {
    return {
      filters: [],
      modalForm: true,
      showForm: false,
    }
  },

  computed: {
    showControls() {
      return !this.showForm || this.modalForm;
    }
  },

  methods: {
    async fetchFilters() {
      let response = await fetch('http://localhost:8080/api/filters');
      this.filters = await response.json();
    },

    stringifyCriterion(criterion) {
      let type = `${criterion.type[0]}${criterion.type.slice(1).toLowerCase()}`;
      let condition = criterion.condition.replace('_', ' ').toLowerCase();
      let value = criterion.value;
      return `${type} ${condition} ${value}`;
    },
  },

  created() {
    this.fetchFilters();
  },
}
</script>
