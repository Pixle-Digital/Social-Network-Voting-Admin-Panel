<template>
  <b-row>
    <b-colxx class="disable-text-selection">
      <list-page-heading
        title="Intents"
        :selectAll="selectAll"
        :isSelectedAll="isSelectedAll"
        :isAnyItemSelected="isAnyItemSelected"
        :selectedItems="selectedItems"
        :rowselected="rowselected"
        :keymap="keymap"
        :displayMode="displayMode"
        :changeDisplayMode="changeDisplayMode"
        :changeOrderBy="changeOrderBy"
        :changeFilterBy="changeFilterBy"
        :changePageSize="changePageSize"
        :sort="sort"
        :filter="filter"
        :searchChange="searchChange"
        :from="from"
        :to="to"
        :total="total"
        :perPage="perPage"
      ></list-page-heading>
      <template v-if="isLoad">
        <list-page-listing
          :displayMode="displayMode"
          :items="items"
          :selectedItems="selectedItems"
          :rowselected="rowselected"
          :toggleItem="toggleItem"
          :lastPage="lastPage"
          :perPage="perPage"
          :page="page"
          :changePage="changePage"
          :handleContextMenu="handleContextMenu"
          :onContextMenuAction="onContextMenuAction"
        ></list-page-listing>
      </template>
      <template v-else>
        <div class="loading"></div>
      </template>
    </b-colxx>
  </b-row>
</template>

<script>
import axios from "axios";
import ListPageHeading from "../../containers/pages/ListPageHeading";
import ListPageListing from "../../containers/pages/ListPageListing";

export default {
  name: "statistics",
  components: {
    "list-page-heading": ListPageHeading,
    "list-page-listing": ListPageListing
  },
  data() {
    return {
      isLoad: false,
      apiBase: "http://localhost:3000/apifortesting",
      displayMode: "list",
      sort: {
        column: "tag",
        label: "Tag"
      },
      filter: {
        column: "tag",
        label: "Tag"
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
      rowselected: []
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
        .get(this.apiUrl, auth, config)
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
    },

    changeDisplayMode(displayType) {
      this.displayMode = displayType;
    },
    changePageSize(perPage) {
      // this.page = 1;
      this.perPage = perPage;
    },
    changeOrderBy(sort) {
      this.sort = sort;
    },
    changeFilterBy(filter){
      this.filter = filter;
      console.log(this.filter)
    },
    searchChange(val) {
      console.log(this.sort.column)
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
        .get(`https://api.komplai.com/Intent/Paginate?page=1&per_page=${this.perPage}&filter_by=${this.filter.column}&query=${val}`, auth, config)
        .then(response => {
          return response.data;
        })
        .then(res => {
        console.log(res);
          this.total = res.total;
          this.from = res.from;
          this.to = res.to;
          this.items = res.data.map((x) => {
            return {
              ...x,
              img: null,
            };
          });
          this.perPage = res.per_page;
          this.selectedItems = [];
          this.lastPage = res.last_page;
          this.isLoad = true;
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
      console.log(itemId)
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
      return `https://api.komplai.com/Intent/Paginate?sort_by=${this.sort.column}&page=${this.page}&per_page=${this.perPage}`;
      // let params = {
      //   params: {
      //     sort_by: this.sort.column,
      //     page: this.page,
      //     per_page: this.perPage
      //   }
      // };
      // return params;
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
