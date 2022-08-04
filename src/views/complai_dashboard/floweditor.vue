<template>
  <div class="container">
    <draggable class="row">
      <b-col cols="8">
        <!-- FLOW CHART AREA HERE  -->
        <b-card>
          <h2>Flow Chart Area Here</h2>
          <div class="container">
            <vue-tree
              style="
                max-width: 700px;
                height: 600px;
                border: 1px solid gray;
                overflow: scroll;
              "
              :dataset="richMediaData"
              :config="treeConfig"
            >
              <template v-slot:node="{ node, collapsed }">
                <div
                  class="rich-media-node"
                  :style="{ border: collapsed ? '2px solid grey' : '' }"
                >
                  <img
                    :src="node.avatar"
                    style="width: 48px; height: 48px; border-raduis: 4px"
                  />
                  <span style="padding: 4px 0; font-weight: bold"
                    >{{ node.value }} {{ node.name }}</span
                  >
                </div>
              </template>
            </vue-tree>
          </div>
        </b-card>
      </b-col>

      <b-col cols="4">
        <b-card>
          <div class="">
            <b-form>
              <b-form-group label="Text" description="">
                <b-form-input
                  type="text"
                  v-model="text"
                  placeholder="Write Text Here..."
                />
              </b-form-group>
              <b-form-group label="Hyperlink">
                <b-form-input type="text" v-model="hyperlink" placeholder="" />
              </b-form-group>
              <b-form-group label="ChildOf">
                <!-- <b-form-input type="text" v-model="childOf" placeholder="" /> -->
                <textarea
                  wrap="soft"
                  class="form-control"
                  v-model="childOf"
                  style="height: 200px"
                  id="__BVID__150"
                  spellcheck="false"
                ></textarea>
              </b-form-group>
            </b-form>
          </div>

          <b-button-group class="w-100">
            <b-button>Preview</b-button>
            <b-button>Edit</b-button>
          </b-button-group>
        </b-card>
      </b-col>
    </draggable>

    <b-row class="mt-5">
      <b-col cols="8"> </b-col>
      <b-col cols="4">
        <b-card>
          <b-form-group label="Introduction Text">
            <textarea
              wrap="soft"
              class="form-control"
              style="height: 200px"
              id="__BVID__150"
              spellcheck="false"
            ></textarea>
          </b-form-group>

          <b-form-group>
            <b-form-checkbox
              id="checkbox-1"
              name="checkbox-1"
              value="accepted"
              unchecked-value="not_accepted"
            >
              Enabled
            </b-form-checkbox>
          </b-form-group>

          <b-form-group label="Second Text">
            <textarea
              wrap="soft"
              class="form-control"
              style="height: 200px"
              id="__BVID__150"
              spellcheck="false"
            ></textarea>
          </b-form-group>

          <b-button-group class="w-100">
            <b-button>Preview</b-button>
            <b-button>Edit</b-button>
          </b-button-group>
        </b-card>
      </b-col>
    </b-row>
  </div>
</template>

<script>
import Draggable from "vuedraggable";

export default {
  name: "floweditor",
  components: {
    draggable: Draggable,
  },
  data() {
    return {
      text: "",
      hyperlink: "",
      childOf: "",
      richMediaData: {
        name: "Start",
        value: 800,
        avatar:
          "https://gravatar.com/avatar/db51fdaf64d942180b5200ca37d155a4?s=400&d=robohash&r=x",
        children: [
          {
            name: "Price",
            value: 400,
            avatar:
              "https://gravatar.com/avatar/16b3b886b837257757c5961513396a06?s=400&d=robohash&r=x",
            children: [
              {
                name: "Price Of Product",
                value: 100,
                avatar:
                  "https://gravatar.com/avatar/4ee8775f23f12755db978cccdc1356d9?s=400&d=robohash&r=x",
                children: [
                  {
                    name: "The Product Is 3$",
                    value: 300,
                    avatar:
                      "https://gravatar.com/avatar/d3efa8fa639bdada96a7d0b4372e0a96?s=400&d=robohash&r=x",
                  },
                ],
              },
              {
                name: "Competitor Prices",
                value: 300,
                avatar:
                  "https://gravatar.com/avatar/d3efa8fa639bdada96a7d0b4372e0a96?s=400&d=robohash&r=x",
              },
              {
                name: "Information Cost",
                value: 200,
                avatar:
                  "https://gravatar.com/avatar/4905bc3e5dc51a61e3b490ccf1891107?s=400&d=robohash&r=x",
              },
            ],
          },
          {
            name: "Smith",
            value: 200,
            avatar:
              "https://gravatar.com/avatar/d05d081dbbb513180025300b715d5656?s=400&d=robohash&r=x",
            children: [
              {
                name: "S1",
                value: 230,
                avatar:
                  "https://gravatar.com/avatar/60c1e69e690d943c5dc06568148debc4?s=400&d=robohash&r=x",
              },
            ],
          },
          {
            name: "Jackson",
            value: 300,
            avatar:
              "https://gravatar.com/avatar/581f7a711c815d9671c35ebd815ec1e4?s=400&d=robohash&r=x",
          },
        ],
      },
      treeConfig: { nodeWidth: 120, nodeHeight: 80, levelHeight: 200 },
    };
  },
};
</script>

<style>
</style>
