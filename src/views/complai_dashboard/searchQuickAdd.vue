<template>
  <b-row>
    <b-col lg="6" md="6">
      <list-page-heading
        title="Intents"
        :selectAll="selectAll"
        :isSelectedAll="isSelectedAll"
        :isAnyItemSelected="isAnyItemSelected"
        :selectedItems="selectedItems"
        :keymap="keymap"
        :displayMode="displayMode"
        :changeDisplayMode="changeDisplayMode"
        :changeOrderBy="changeOrderBy"
        :changeorder="changeorder"
        :sort="sort"
        :changeFilterBy="changeFilterBy"
        @intentPageSizeChanged="capturePageSizeChange($event)"
        :filter="filter"
        :searchChange="searchChange"
        :from="from"
        :to="to"
        :total="total"
        :perPage="perPage"
        :hideSelectedIntents="hideSelectedIntents"
      ></list-page-heading>
      <template v-if="isLoad">
        <list-page-listing
          :displayMode="displayMode"
          :items="items"
          :selectedItems="selectedItems"
          :selectedItems1="selectedItems1"
          :toggleItem="toggleItem"
          :lastPage="lastPage"
          :perPage="perPage"
          :page="page"
          :changePage="changePage"
          @hide:items="eventCaptured($event)"
          @Unhide:items="UnhideIntentCapture($event)"
        ></list-page-listing>
        <!-- :handleContextMenu="handleContextMenu" -->
        <!-- :onContextMenuAction="onContextMenuAction" -->
      </template>
      <template v-else>
        <div class="loading"></div>
      </template>
    </b-col>
    <b-col lg="6" md="6">
      <list-page-heading-source
        title="Sources"
        :selectAll="selectAll"
        :isSelectedAll="isSelectedAll1"
        :isAnyItemSelected="isAnyItemSelected1"
        :keymap="keymap"
        :selectedItems="selectedItems1"
        :displayMode="displayMode"
        :changeDisplayMode="changeDisplayMode"
        :changeorderSource="changeorderSource"
        :changeOrderBy1="changeOrderBy1"
        :changePageSize="changePageSize1"
        :sort1="sort1"
        :searchChangesource="searchChangesource"
        :changeFilterBy1="changeFilterBy1"
        :filter1="filter1"
        :from1="from1"
        :to1="to1"
        :total1="total1"
        :perPage1="perPage1"
      ></list-page-heading-source>

      <template v-if="isLoad1">
        <list-page-listing-source
          :displayMode="displayMode"
          :items="items1"
          :selectedItems="selectedItems"
          :toggleItem="toggleItem"
          :lastPage="lastPage1"
          :perPage="perPage1"
          :dataFromIntent="getIntentData"
          :page="page1"
          :changePage="changePage1"
          @hideSource:items="hideSourceCapture($event)"
          @UnhideSource:items="UnhideSourceCapture($event)"
        ></list-page-listing-source>
        <!-- :handleContextMenu="handleContextMenu1" -->
        <!-- :onContextMenuAction="onContextMenuAction1" -->
      </template>
      <template v-else>
        <div class="loading"></div>
      </template>
    </b-col>
  </b-row>

  <!-- <b-colxx lg="4" md="6" class="mb-4">
        <logs></logs>
      </b-colxx>  -->

  <!-- <b-colxx lg="4" md="6" class="mb-4">
        <logs></logs>
      </b-colxx> -->
</template>

<script>
import axios from "axios";
import ListPageHeading from "../../containers/pages/ListPageHeading";
import ListPageListing from "../../containers/pages/ListPageListing";
import ListPageHeadingsource from "../../containers/pages/ListPageHeadingsource";
import ListPageListingsource from "../../containers/pages/ListPageListingsource";
import Logs from "../../containers/dashboards/Logs";
import ColorSwitcherVue from "../../components/Common/ColorSwitcher.vue";
import { mapGetters } from "vuex";

export default {
  name: "statistics",
  components: {
    "list-page-heading": ListPageHeading,
    "list-page-listing": ListPageListing,
    logs: Logs,
    "list-page-heading-source": ListPageHeadingsource,
    "list-page-listing-source": ListPageListingsource
  },
  data() {
    return {
      order: "",
      getIntentData: null,
      gsourceid: "",
      gtag: "",
      isLoad: false,
      displayMode: "list",
      sort: {
        column: "tag",
        label: "Tag"
      },
      filter: {
        column: "all",
        label: "All"
      },
      page: 1,
      perPage: 5,
      search: "",
      from: 0,
      to: 0,
      total: 0,
      lastPage: 0,
      items: [],
      selectedItems: [],
      selectedItems1: [],
      sort1: {
        column: "source_id",
        label: "Source ID"
      },
      filter1: {
        column: "all",
        label: "All"
      },
      isLoad1: false,
      page1: 1,
      perPage1: 5,
      search1: "",
      from1: 0,
      to1: 0,
      total1: 0,
      lastPage1: 0,
      items1: [],
      selectedItems1: [],
      hiddenGroups: [],
      tableRendered: false
    };
  },
  methods: {
    capturePageSizeChange(value) {
      console.log("Page Size Changed", value);
      this.perPage = value;
    },
    // Hide/Unhide
    hideSelectedIntents() {
      console.log("hideSelectedIntents -> ", this.getSelectedIntent);
      if (this.getSelectedIntent.length == 0) {
        swal({
          title: "No Intent Selected",
          text: `No Intent Selected`,
          icon: "warning"
        });
      } else if (this.getSelectedIntent.length == 1) {
        let singleGroup = this.getSelectedIntent[0].group;

        let hideApiUri = `https://api.komplai.com/Intent/Paginate?hide=${singleGroup}&hide_by=group`;

        let auth = {
          auth: {
            username: "operator",
            password: "supersecret"
          }
        };

        axios
          .get(hideApiUri, auth)
          .then(response => {
            const newData = response.data;
            let hidden = response.data.hidden;

            hidden.forEach(group => this.hiddenGroups.push(group));
            console.log("Hide Intent Api Response", response.data);

            this.total = newData.total;
            this.from = newData.from;
            this.to = newData.to;
            this.items = newData.data.map(x => {
              return {
                ...x,
                img: null
              };
            });
            this.perPage = newData.per_page;
            this.selectedItems = [];
            this.lastPage = newData.last_page;
            this.isLoad = true;
          })
          .catch(err => console.log("from Hide Api", err));
        swal({
          title: "1 Intent Selected",
          text: `1 Intent Selected`,
          icon: "warning"
        });
      } else {
        swal({
          title: "more than 1 Intent Selected",
          text: `more than 1 Intent Selected`,
          icon: "warning"
        });
      }
    },
    loadItems() {
      // console.log(this.$root);

      this.isLoad = false;

      let config = {
        headers: {
          "Access-Control-Allow-Origin": "*",
          "Access-Control-Allow-Credentials": "true",
          "Access-Control-Allow-Methods": "GET,HEAD,OPTIONS,POST,PUT",
          "Access-Control-Allow-Headers":
            "Access-Control-Allow-Headers, Origin,Accept, X-Requested-With, Content-Type, Access-Control-Request-Method, Access-Control-Request-Headers"
        }
      };

      let auth = {
        auth: {
          username: "operator",
          password: "supersecret"
        }
      };

      axios
        .get(this.apiUrl, auth)
        .then(response => {
          return response.data;
        })
        .then(res => {
          console.log("Load Items", res);
          this.total = res.total;
          this.from = res.from;
          this.to = res.to;
          this.items = res.data.map(x => {
            return {
              ...x,
              img: null
            };
          });
          this.perPage = res.per_page;
          this.selectedItems = [];
          this.lastPage = res.last_page;
          this.isLoad = true;
        });
    },
    loadItemssource() {
      this.isLoad1 = false;
      let config = {
        headers: {
          "Access-Control-Allow-Origin": "*",
          "Access-Control-Allow-Credentials": "true",
          "Access-Control-Allow-Methods": "GET,HEAD,OPTIONS,POST,PUT",
          "Access-Control-Allow-Headers":
            "Access-Control-Allow-Headers, Origin,Accept, X-Requested-With, Content-Type, Access-Control-Request-Method, Access-Control-Request-Headers"
        }
      };

      let auth = {
        auth: {
          username: "operator",
          password: "supersecret"
        }
      };

      axios
        .get(this.apiUrl1, auth, config)
        .then(response => {
          return response.data;
        })
        .then(res => {
          console.log("Load Items Source", res);
          this.total1 = res.total;
          this.from1 = res.from;
          this.to1 = res.to;
          this.items1 = res.data.map(x => {
            return {
              ...x,
              img: null
            };
          });
          this.perPage1 = res.per_page;
          this.selectedItems1 = [];
          this.lastPage1 = res.last_page;
          this.isLoad1 = true;
        });
    },
    eventCaptured(newData) {
      this.$nextTick(() => {
        if (newData) {
          // console.log("Something Happenede :", newData);

          this.total = newData.total;
          this.from = newData.from;
          this.to = newData.to;
          this.items = newData.data.map(x => {
            return {
              ...x,
              img: null
            };
          });
          this.perPage = newData.per_page;
          this.selectedItems = [];
          this.lastPage = newData.last_page;
          this.isLoad = true;
        }
      });
    },
    UnhideIntentCapture(newData) {
      if (newData) {
        // console.log("Unhide Intent Occured :", newData);

        this.total = newData.total;
        this.from = newData.from;
        this.to = newData.to;
        this.items = newData.data.map(x => {
          return {
            ...x,
            img: null
          };
        });
        this.perPage = newData.per_page;
        this.selectedItems = [];
        this.lastPage = newData.last_page;
        this.isLoad = true;
      }
    },
    hideSourceCapture(newSourceData) {
      if (newSourceData) {
        // console.log("Source Hidden :", newSourceData);

        this.total1 = newSourceData.total;
        this.from1 = newSourceData.from;
        this.to1 = newSourceData.to;
        this.items1 = newSourceData.data.map(x => {
          return {
            ...x,
            img: null
          };
        });
        this.perPage1 = newSourceData.per_page;
        this.selectedItems1 = [];
        this.lastPage1 = newSourceData.last_page;
        this.isLoad1 = true;
      }
    },
    UnhideSourceCapture(newSourceData) {
      if (newSourceData) {
        // console.log("Source Hidden :", newSourceData);

        this.total1 = newSourceData.total;
        this.from1 = newSourceData.from;
        this.to1 = newSourceData.to;
        this.items1 = newSourceData.data.map(x => {
          return {
            ...x,
            img: null
          };
        });
        this.perPage1 = newSourceData.per_page;
        this.selectedItems1 = [];
        this.lastPage1 = newSourceData.last_page;
        this.isLoad1 = true;
      }
    },
    changeDisplayMode(displayType) {
      this.displayMode = displayType;
    },
    // changePageSize(perPage) {
    //   // this.page = 1;
    //   this.perPage = perPage;
    // },
    changePageSize1(perPage1) {
      // this.page = 1;
      this.perPage1 = perPage1;
    },
    changeorder(order, name) {
      console.log(order, name);
      if (order == true) {
        let config = {
          headers: {
            "Access-Control-Allow-Origin": "*",
            "Access-Control-Allow-Credentials": "true",
            "Access-Control-Allow-Methods": "GET,HEAD,OPTIONS,POST,PUT",
            "Access-Control-Allow-Headers":
              "Access-Control-Allow-Headers, Origin,Accept, X-Requested-With, Content-Type, Access-Control-Request-Method, Access-Control-Request-Headers"
          }
        };

        let auth = {
          auth: {
            username: "operator",
            password: "supersecret"
          }
        };

        axios
          .get(
            `https://api.komplai.com/Intent/Paginate?sort_by=${name}&order=desc`,
            auth,
            config
          )
          .then(response => {
            return response.data;
          })
          .then(res => {
            console.log(res);
            this.total = res.total;
            this.from = res.from;
            this.to = res.to;
            this.items = res.data.map(x => {
              return {
                ...x,
                img: null
              };
            });
            this.perPage = res.per_page;
            this.selectedItems = [];
            this.rowselected = [];
            this.lastPage = res.last_page;
            this.isLoad = true;
          });
      }
      if (order == false) {
        let config = {
          headers: {
            "Access-Control-Allow-Origin": "*",
            "Access-Control-Allow-Credentials": "true",
            "Access-Control-Allow-Methods": "GET,HEAD,OPTIONS,POST,PUT",
            "Access-Control-Allow-Headers":
              "Access-Control-Allow-Headers, Origin,Accept, X-Requested-With, Content-Type, Access-Control-Request-Method, Access-Control-Request-Headers"
          }
        };

        let auth = {
          auth: {
            username: "operator",
            password: "supersecret"
          }
        };

        axios
          .get(
            `https://api.komplai.com/Intent/Paginate?sort_by=${this.sort.column}&order=asc`,
            auth,
            config
          )
          .then(response => {
            return response.data;
          })
          .then(res => {
            console.log(res);
            this.total = res.total;
            this.from = res.from;
            this.to = res.to;
            this.items = res.data.map(x => {
              return {
                ...x,
                img: null
              };
            });
            this.perPage = res.per_page;
            this.selectedItems = [];
            this.rowselected = [];
            this.lastPage = res.last_page;
            this.isLoad = true;
          });
      }
    },
    changeorderSource(order, name) {
      console.log(order, name);
      if (order == true) {
        let config = {
          headers: {
            "Access-Control-Allow-Origin": "*",
            "Access-Control-Allow-Credentials": "true",
            "Access-Control-Allow-Methods": "GET,HEAD,OPTIONS,POST,PUT",
            "Access-Control-Allow-Headers":
              "Access-Control-Allow-Headers, Origin,Accept, X-Requested-With, Content-Type, Access-Control-Request-Method, Access-Control-Request-Headers"
          }
        };

        let auth = {
          auth: {
            username: "operator",
            password: "supersecret"
          }
        };

        axios
          .get(
            `https://api.komplai.com/Source/Paginate?sort_by=${name}&order=desc`,
            auth,
            config
          )
          .then(response => {
            return response.data;
          })
          .then(res => {
            console.log(res);
            this.total = res.total;
            this.from1 = res.from;
            this.to1 = res.to;
            this.items1 = res.data.map(x => {
              return {
                ...x,
                img: null
              };
            });
            this.perPage1 = res.per_page;
            this.selectedItems1 = [];
            this.rowselected1 = [];
            this.lastPage1 = res.last_page;
            this.isLoad1 = true;
          });
      }
      if (order == false) {
        let config = {
          headers: {
            "Access-Control-Allow-Origin": "*",
            "Access-Control-Allow-Credentials": "true",
            "Access-Control-Allow-Methods": "GET,HEAD,OPTIONS,POST,PUT",
            "Access-Control-Allow-Headers":
              "Access-Control-Allow-Headers, Origin,Accept, X-Requested-With, Content-Type, Access-Control-Request-Method, Access-Control-Request-Headers"
          }
        };

        let auth = {
          auth: {
            username: "operator",
            password: "supersecret"
          }
        };

        axios
          .get(
            `https://api.komplai.com/Source/Paginate?sort_by=${this.sort1.column}&order=asc`,
            auth
          )
          .then(response => {
            return response.data;
          })
          .then(res => {
            console.log(res);
            this.total1 = res.total;
            this.from1 = res.from;
            this.to1 = res.to;
            this.items1 = res.data.map(x => {
              return {
                ...x
              };
            });
            this.perPage1 = res.per_page;
            this.selectedItems1 = [];
            this.rowselected1 = [];
            this.lastPage1 = res.last_page;
            this.isLoad1 = true;
          });
      }
    },
    changeOrderBy(sort) {
      this.sort = sort;
      console.log(this.sort);
      if (this.sort.order == "asc") {
        let config = {
          headers: {
            "Access-Control-Allow-Origin": "*",
            "Access-Control-Allow-Credentials": "true",
            "Access-Control-Allow-Methods": "GET,HEAD,OPTIONS,POST,PUT",
            "Access-Control-Allow-Headers":
              "Access-Control-Allow-Headers, Origin,Accept, X-Requested-With, Content-Type, Access-Control-Request-Method, Access-Control-Request-Headers"
          }
        };

        let auth = {
          auth: {
            username: "operator",
            password: "supersecret"
          }
        };

        axios
          .get(
            `https://api.komplai.com/Intent/Paginate?sort_by=${this.sort.column}&order=asc`,
            auth,
            config
          )
          .then(response => {
            return response.data;
          })
          .then(res => {
            console.log(res);
            this.total = res.total;
            this.from = res.from;
            this.to = res.to;
            this.items = res.data.map(x => {
              return {
                ...x,
                img: null
              };
            });
            this.perPage = res.per_page;
            this.selectedItems = [];
            this.rowselected = [];
            this.lastPage = res.last_page;
            this.isLoad = true;
          });
      }
      if (this.sort.order == "desc") {
        let config = {
          headers: {
            "Access-Control-Allow-Origin": "*",
            "Access-Control-Allow-Credentials": "true",
            "Access-Control-Allow-Methods": "GET,HEAD,OPTIONS,POST,PUT",
            "Access-Control-Allow-Headers":
              "Access-Control-Allow-Headers, Origin,Accept, X-Requested-With, Content-Type, Access-Control-Request-Method, Access-Control-Request-Headers"
          }
        };

        let auth = {
          auth: {
            username: "operator",
            password: "supersecret"
          }
        };

        axios
          .get(
            `https://api.komplai.com/Intent/Paginate?sort_by=${this.sort.column}&order=desc`,
            auth,
            config
          )
          .then(response => {
            return response.data;
          })
          .then(res => {
            console.log(res);
            this.total = res.total;
            this.from = res.from;
            this.to = res.to;
            this.items = res.data.map(x => {
              return {
                ...x,
                img: null
              };
            });
            this.perPage = res.per_page;
            this.selectedItems = [];
            this.rowselected = [];
            this.lastPage = res.last_page;
            this.isLoad = true;
          });
      }
    },
    changeFilterBy(filter) {
      this.filter = filter;
      console.log(this.filter);
    },
    changeFilterBy1(filter) {
      console.log(filter);
      this.filter1 = filter;
    },
    changeOrderBy1(sort) {
      console.log(sort);
      this.sort1 = sort;
    },
    searchChangesource(val) {
      console.log(val);
      let config = {
        headers: {
          "Access-Control-Allow-Origin": "*",
          "Access-Control-Allow-Credentials": "true",
          "Access-Control-Allow-Methods": "GET,HEAD,OPTIONS,POST,PUT",
          "Access-Control-Allow-Headers":
            "Access-Control-Allow-Headers, Origin,Accept, X-Requested-With, Content-Type, Access-Control-Request-Method, Access-Control-Request-Headers"
        }
      };

      let auth = {
        auth: {
          username: "operator",
          password: "supersecret"
        }
      };

      axios
        .get(
          `https://api.komplai.com/Source/Paginate?page=1&per_page=10&filter_by=${this.filter1.column}&query=${val}`,
          auth
        )
        .then(response => {
          return response.data;
        })
        .then(res => {
          if (res == 404) {
            console.clear();
            this.items1 = {};
          }

          this.total1 = res.total;
          this.from1 = res.from;
          this.to1 = res.to;
          this.items1 = res.data;
          this.perPage1 = res.per_page;
          this.selectedItems1 = [];
          this.rowselected1 = [];
          this.lastPage1 = res.last_page;
          this.isLoad1 = true;
        })
        .catch(err => {
          this.items1 = {};
        });
    },
    searchChange(val) {
      console.log(val);
      this.search = val;
      let config = {
        headers: {
          "Access-Control-Allow-Origin": "*",
          "Access-Control-Allow-Credentials": "true",
          "Access-Control-Allow-Methods": "GET,HEAD,OPTIONS,POST,PUT",
          "Access-Control-Allow-Headers":
            "Access-Control-Allow-Headers, Origin,Accept, X-Requested-With, Content-Type, Access-Control-Request-Method, Access-Control-Request-Headers"
        }
      };

      let auth = {
        auth: {
          username: "operator",
          password: "supersecret"
        }
      };

      axios
        .get(
          `https://api.komplai.com/Intent/Paginate?page=1&per_page=10&filter=${this.filter.column}&query=${val}`,
          auth,
          config
        )
        .then(response => {
          console.log(response);

          return response.data;
        })
        .then(res => {
          // console.log(res);
          if (res == 404) {
            console.clear();
            // console.log("Search Method:", res);
            this.items = {};
          } else {
            this.total = res.total;
            this.from = res.from;
            this.to = res.to;
            this.items = res.data;
            this.perPage = res.per_page;
            this.selectedItems = [];
            this.rowselected = [];
            this.lastPage = res.last_page;
            this.isLoad = true;
          }
        })
        .catch(err => {
          // console.log("Error From Catch", err);
          this.items = {};
        });

      // this.page = 1;
    },
    selectAll(isToggle) {
      if (this.selectedItems.length >= this.items.length) {
        if (isToggle) this.selectedItems = [];
      } else {
        this.selectedItems = this.items.map(x => x.tag);
      }
    },
    keymap(event) {
      switch (event.srcKey) {
        case "select":
          this.selectAll(false);
          break;
        case "undo":
          this.selectedItems = [];
          break;
      }
    },
    getIndex(value, arr, prop) {
      for (var i = 0; i < arr.length; i++) {
        if (arr[i][prop] === value) {
          return i;
        }
      }
      return -1;
    },
    toggleItem(event, itemId) {
      // this.gtag = itemId.tag
      // this.gsourceid = itemId.source_id
      //  if (this.gtag == undefined && this.gsourceid != "") {
      //    swal({
      //     title: "Are you sure?",
      //     text: "Do you want to connect this Data!",
      //     icon: "warning",
      //     buttons: true,
      //     dangerMode: true,
      //   })
      //  }
      if (event.shiftKey && this.selectedItems.length > 0) {
        let itemsForToggle = this.items;
        var start = this.getIndex(itemId, itemsForToggle, "id");
        var end = this.getIndex(
          this.selectedItems[this.selectedItems.length - 1],
          itemsForToggle,
          "tag"
        );
        itemsForToggle = itemsForToggle.slice(
          Math.min(start, end),
          Math.max(start, end) + 1
        );
        this.selectedItems.push(
          ...itemsForToggle.map(item => {
            return item.tag;
          })
        );
      } else {
        if (this.selectedItems.includes(itemId)) {
          this.selectedItems = this.selectedItems.filter(x => x !== itemId);
        } else this.selectedItems.push(itemId);
      }
    },
    // handleContextMenu1(vnode) {
    //   console.log("This is vnode key: ", vnode.key);
    // },
    // onContextMenuAction1(action) {
    //   console.log(
    //     "context menu item clicked - " + action + ": "
    //     // this.selectedItems
    //   );
    //   // console.log("Data from context method", data);
    // },
    changePage(pageNum) {
      this.page = pageNum;
    },
    changePage1(pageNum) {
      this.page1 = pageNum;
    },
    dataWatcher() {
      // Code won't execute after this.data assignment at the api promise resolution
      // this.hideSelectedIntents();
      console.log("Data Came !");
    }
  },

  computed: {
    ...mapGetters(["getSelectedIntent"]),
    isSelectedAll() {
      return this.selectedItems.length >= this.items.length;
    },
    isAnyItemSelected() {
      return (
        this.selectedItems.length > 0 &&
        this.selectedItems.length < this.items.length
      );
    },
    isSelectedAll1() {
      return this.selectedItems1.length >= this.items1.length;
    },
    isAnyItemSelected1() {
      return (
        this.selectedItems1.length > 0 &&
        this.selectedItems1.length < this.items1.length
      );
    },
    apiUrl() {
      return `https://api.komplai.com/Intent/Paginate?sort_by=${this.sort.column}&page=${this.page}&per_page=${this.perPage}`;
    },
    apiUrl1() {
      return `https://api.komplai.com/Source/Paginate?sort_by=${this.sort1.column}&page=${this.page1}&per_page=${this.perPage1}`;
    }
  },
  watch: {
    search() {
      this.page = 1;
    }
    // apiUrl() {
    //   this.loadItems();
    // },
    // apiUrl1() {
    //   this.loadItemssource();
    // }
  },
  beforeCreate() {},
  created() {},
  mounted() {
    // this.$root.$on("hide:items", function(value) {
    //   console.log("Root Event Captured hide:items -> ", value);
    // });
    this.loadItems();
    this.loadItemssource();
    // this.$watch("items", this.dataWatcher);
  }
};
</script>
