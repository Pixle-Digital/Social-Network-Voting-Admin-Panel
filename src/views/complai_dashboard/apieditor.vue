<template>
  <div class="">
    <h4 class="text-muted">API EDITOR</h4>
    <draggable class="row">
      <b-col cols="8">
        <b-card>
          <h4 class="my-4">Available Program</h4>
          <b-table
            striped
            hover
            :items="bootstrapTable.items"
            :fields="bootstrapTable.fields"
          >
            <template #cell(enable)="data">
              <b-form-checkbox></b-form-checkbox>
            </template>
          </b-table>
          <b-button class="mb-4">Upload</b-button>
          <b-button class="mb-4" variant="light">?</b-button>

          <h4 class="my-5">Available API's</h4>
          <b-table
            striped
            hover
            :items="bootstrapTable2.items"
            :fields="bootstrapTable2.fields"
          >
            <template #cell(enable)="data">
              <b-form-checkbox></b-form-checkbox>
            </template>
          </b-table>
          <b-button>Upload</b-button>
          <b-button variant="light">?</b-button>
        </b-card>
      </b-col>
      <b-col cols="4">
        <b-card class="human-activity">
          <h4>Activity</h4>
          <hr />
          <h6>Human Interact - Has Encountered an error</h6>
        </b-card>
      </b-col>
    </draggable>
  </div>
</template>

<script>
import Draggable from "vuedraggable";

export default {
  name: "apiEditor",
  components: {
    draggable: Draggable,
  },
  data() {
    return {
      bootstrapTable: {
        items: [
          {
            name: "Dickerson",
            description: "Macdonald is a good place !",
            enable: "Hello",
          },
        ],
        fields: [
          {
            key: "name",
            label: "Name",
            sortable: true,
            sortDirection: "desc",
            tdClass: "list-item-heading",
          },
          {
            key: "description",
            label: "Description",
            sortable: true,
            tdClass: "text-muted",
          },
          {
            key: "enable",
            label: "Enable",
            sortable: true,
            tdClass: "text-muted",
          },
        ],
      },
      bootstrapTable2: {
        items: [
          {
            name: "Population Census",
            description: "API connection to the global census !",
          },
          {
            name: "Gambling Data",
            description: "API connection to the global census !",
          },
          {
            name: "Stock Data",
            description: "API connection to the global census !",
          },
        ],
        fields: [
          {
            key: "name",
            label: "Name",
            sortable: true,
            sortDirection: "desc",
            tdClass: "list-item-heading",
          },
          {
            key: "description",
            label: "Description",
            sortable: true,
            tdClass: "text-muted",
          },
        ],
      },
    };
  },
};
</script>

<style>
.human-activity {
  min-height: 600px;
  max-height: 620px;
}
</style>
