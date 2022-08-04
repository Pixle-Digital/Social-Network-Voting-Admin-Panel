<template>
  <b-row>
    <b-colxx class="disable-text-selection">


    </b-colxx>
  </b-row>
</template>

<script>
import axios from "axios";

export default {
  name: "source",
  components: {
  },
  data() {
    return {
      isLoad: false,
      apiBase: "http://localhost:3000/apifortesting",
      displayMode: "list",
      sort1: {
        column: "source_id",
        label: "Source ID"
      },
      filter1: {
        column: "source_id",
        label: "Source ID"
      },
      page: 1,
      perPage: 5,
      search: "",
      from: 0,
      to: 0,
      total: 0,
      lastPage: 0,
      items: [],
      selectedItems: []
    };
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
          this.lastPage = res.last_page;
          this.isLoad = true;
        });
    },

    changeDisplayMode(displayType) {
      this.displayMode = displayType;
    },
    changePageSize(perPage) {
      // this.page = 1;
      this.perPage = perPage;
    },
    changeOrderBy1(sort) {
      this.sort1 = sort;
    },
    changeFilterBy1(filter) {
      this.filter1 = filter;
    },

      searchChangesource(val) {
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
        .get(`https://api.komplai.com/Source/Paginate?page=1&per_page=${this.perPage}&filter_by=${this.filter1.column}&query=${val}`, auth, config)
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





      // this.page = 1;
    },

    selectAll(isToggle) {
      if (this.selectedItems.length >= this.items.length) {
        if (isToggle) this.selectedItems = [];
      } else {
        this.selectedItems = this.items.map(x => x.source_id);
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
      console.log(itemId + "" + event)
      if (event.shiftKey && this.selectedItems.length > 0) {
        let itemsForToggle = this.items;
        var start = this.getIndex(itemId, itemsForToggle, "id");
        var end = this.getIndex(
          this.selectedItems[this.selectedItems.length - 1],
          itemsForToggle,
          "source_id"
        );
        itemsForToggle = itemsForToggle.slice(
          Math.min(start, end),
          Math.max(start, end) + 1
        );
        this.selectedItems.push(
          ...itemsForToggle.map(item => {
            return item.source_id;
          })
        );
      } else {
        if (this.selectedItems.includes(itemId)) {
          this.selectedItems = this.selectedItems.filter(x => x !== itemId);
        } else this.selectedItems.push(itemId);
      }
    },
    handleContextMenu(vnode) {
      if (!this.selectedItems.includes(vnode.key)) {
        this.selectedItems = [vnode.key];
      }
    },
    onContextMenuAction(action) {
      console.log(
        "context menu item clicked - " + action + ": ",
        this.selectedItems
      );
    },
    changePage(pageNum) {
      this.page = pageNum;
    }
  },
  computed: {
    isSelectedAll() {
      return this.selectedItems.length >= this.items.length;
    },
    isAnyItemSelected() {
      return (
        this.selectedItems.length > 0 &&
        this.selectedItems.length < this.items.length
      );
    },
    apiUrl() {
      return `https://api.komplai.com/Source/Paginate?sort_by=${this.sort1.column}&page=${this.page}&per_page=${this.perPage}`;

    }
  },
  watch: {
    search() {
      this.page = 1;
    },
    apiUrl() {
      this.loadItems();
    }
  },
  mounted() {
    this.loadItems();
  }
};
</script>
