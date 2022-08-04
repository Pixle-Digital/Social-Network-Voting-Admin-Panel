<template>
  <div class="">
    <draggable class="row">
      
      <b-col cols="3">
        <b-card>
          <vue-perfect-scrollbar
            class="scroll"
            :settings="{ suppressScrollX: true, wheelPropagation: false }"
            ref="chat-area"
          >
            <div class="chat-area" style="min-height: 123px;">
              <div class="message" v-if="this.response != ''">
                <b-card>
                  <h5>Komplai Bot</h5>
                  <p>{{ this.response}}</p>
                  <!-- <p>2:33PM</p> -->
                </b-card>
              </div>
              <div class="message" v-else>
                <b-card>
                  <h5>Komplai Bot</h5>
                  <p> Search Something</p>
                  <!-- <p>2:33PM</p> -->
                </b-card>
              </div>

             

             

             
            </div>
          </vue-perfect-scrollbar>
          <b-input-group class="mt-3">
            <b-form-input type="text" v-model="search" placeholder="Type your search query here...!"
            ></b-form-input>
            <b-input-group-append>
              <b-button variant="info" @click="loadItems()" squared>Search</b-button>
            </b-input-group-append>
          </b-input-group>
        </b-card>
      </b-col>

      <b-col cols="3">
        <b-card>
          <div class="information">
            <h5>SoftMax : {{ this.softMax }}</h5>
            <h5>PredictedTag : {{ this.predictedTag }}</h5>
            <h5>TextMatch : {{ this.textMatch }}</h5>
            <h5>Api : {{ this.api == null ? "Null" : "Not Null" }}</h5>
          </div>
        </b-card>
      </b-col>

        <b-col cols="3">
        <b-card>
          <b-row>
            <b-col
              cols="12"
              class="d-flex flex-row justify-content-between align-items-center"
            >
              <div class="items">Tag: Greeting Item:Add</div>
              <div class="">
                <b-button class="inline" size="sm">Upload</b-button>
              </div>
            </b-col>
          </b-row>

          <b-row class="my-4">
            <b-col
              cols="12"
              class="d-flex flex-row justify-content-between align-items-center"
            >
              <div class="items">Tag: Emotion Item:Edit</div>
              <div class="">
                <b-button class="inline" size="sm">Upload</b-button>
              </div>
            </b-col>
          </b-row>

          <b-row class="my-4">
            <b-col
              cols="12"
              class="d-flex flex-row justify-content-between align-items-center"
            >
              <div class="items">Tag: Emotion Item:Add</div>
              <div class="">
                <b-progress
                  :value="loadingState"
                  :max="max"
                  height="2rem"
                  show-progress
                  animated
                ></b-progress>
              </div>
            </b-col>
          </b-row>

          <b-button-group class="w-100">
            <b-button>Upload</b-button>
            <b-button>Log</b-button>
          </b-button-group>
        </b-card>

      </b-col>

      <b-col cols="3">
      

        <b-card class="mt-2">
          <div class="">
            <div class="dayRecord">
              <h5 class="my-2">Date: 03/03/2021</h5>
              <div class="my-2">
                Tag: Greeting , Item: Add , Time: 02:20pm , Pattern: "Hey How's
                its going ?"
              </div>
              <div class="my-2">Tag: Greeting , Item: Add , Time: 02:20pm</div>
            </div>

            <div class="dayRecord">
              <h5 class="my-2">Date: 03/05/2021</h5>
              <div class="my-2">Tag: Greeting , Item: Add</div>
            </div>
          </div>

          <b-button-group class="w-100 mt-4">
            <b-button>Upload</b-button>
            <b-button>Log</b-button>
          </b-button-group>
        </b-card>
      </b-col>
    </draggable>

    <!-- <b-row>
      <b-col cols="3">
        <b-card>
          <div class="">
            <div class="dayRecord">
              <h5 class="my-2">Date: 03/03/2021</h5>
              <div class="my-2">
                Tag: Greeting , Item: Add , Time: 02:20pm , Pattern: "Hey How's
                its going ?"
              </div>
              <div class="my-2">Tag: Greeting , Item: Add , Time: 02:20pm</div>
            </div>

            <div class="dayRecord">
              <h5 class="my-2">Date: 03/05/2021</h5>
              <div class="my-2">Tag: Greeting , Item: Add</div>
            </div>
          </div>

          <b-button-group class="w-100 mt-4">
            <b-button>Upload</b-button>
            <b-button>Log</b-button>
          </b-button-group>
        </b-card>
      </b-col>
    </b-row> -->
  </div>
</template>

<script>
import Draggable from "vuedraggable";
import { mapGetters } from "vuex";
import axios from "axios";

export default {
  name: "testing",
  components: {
    draggable: Draggable,
  },
  data() {
    return {
      api: null,
      textMatch: true,
      predictedTag: "Greetings",
      softMax: 0.7,
      loadingState: 50,
      max: 100,
      search: "",
      response: ""
    };
  },
  computed: {
    ...mapGetters(["getComplaiChat"]),
  },
  methods: {
    loadItems() {
      this.isLoad = false;
      let config = {
        headers: {
          "Access-Control-Allow-Origin": "*",
          "Access-Control-Allow-Credentials": "true",
          "Access-Control-Allow-Methods": "GET,HEAD,OPTIONS,POST,PUT",
          "Access-Control-Allow-Headers":
            "Access-Control-Allow-Headers, Origin,Accept, X-Requested-With, Content-Type, Access-Control-Request-Method, Access-Control-Request-Headers",
        },
      };

      let auth = {
        auth: {
          username: "operator",
          password: "supersecret",
        },
      };

      axios
        .get(`https://api.komplai.com/Chat?query=${this.search}`, auth, config)
        .then((response) => {
          return response.data;
        })
        .then((res) => {
          console.log(res);
          this.response = res.Response
          
        });
    },
  },
  watch: {
   
  },
  mounted() {
  
  },
};
</script>

<style>
.chat-area {
  min-height: 300px;
  max-height: 320px;

  scrollbar-width: 2px;
}
</style>
