<template>
  <div class="container">
    <draggable class="row">
      <b-col cols="8">
        <b-card>
          <h2>Announcements</h2>
          <textarea
            wrap="soft"
            class="form-control"
            style="height: 200px"
            id="__BVID__150"
            spellcheck="false"
          ></textarea>
          <b-button class="my-4">Schedule</b-button>

          <!-- Schedules Table -->
          <h2>Schedules</h2>

          <b-card class="my-4" title="Schedules">
            <b-table :items="items" />
          </b-card>
        </b-card>
      </b-col>
      <b-col cols="4" class="">
        <b-card>
          <h2>Start Text</h2>
          <textarea
            wrap="soft"
            class="form-control"
            style="height: 200px"
            id="__BVID__150"
            spellcheck="false"
          ></textarea>

          <h2 class="my-4">Chat Process Order</h2>
          <textarea
            wrap="soft"
            class="form-control"
            style="height: 200px"
            id="__BVID__150"
            spellcheck="false"
          ></textarea>
        </b-card>
      </b-col>
    </draggable>

    <!-- <b-row class="mt-4 d-flex flex-row-reverse">
      <b-button>Schedule</b-button>
    </b-row> -->
  </div>
</template>

<script>
import Draggable from "vuedraggable";

export default {
  name: "initialisation",
  data() {
    return {
      items: [
        {
          Description: "The LCCP has changed....",
          DatePosted: "10/30/2020 10:00GMT",
        },
        {
          Description: "Complai Maintainance Downtime Expected....",
          DatePosted: "15/30/2020 03:00GMT",
        },
      ],
    };
  },
  components: {
    draggable: Draggable,
  },
};
</script>

<style>
</style>
