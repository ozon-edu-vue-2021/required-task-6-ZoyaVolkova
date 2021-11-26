<template>
  <div v-if="true">
    <div>
      <div class="row row_header">
        <span class="cell cell_id" id="id">
          <p>ID</p>
          <font-awesome-icon
            class="icon_sort"
            icon="sort"
            @click="toggleNumberSort" />

          <v-dropdown
            class="icon_filter"
            :triggers="[]"
            :autoHide="false"
            :shown="this.filterProp === 'id'"
            top-start
          >
            <font-awesome-icon icon="filter" @click="openFilterTooltip" />

            <div slot="popper">
              <input
                class="popperInput"
                type="text"
                v-model="filterText"
                @input="searchText"
              />
              <font-awesome-icon
                icon="times"
                class="closeIcon"
                @click="closeFilterTooltip"
              />
            </div> </v-dropdown
        ></span>

        <span class="cell cell_postId" id="postId">
          <p>Post ID</p>

          <font-awesome-icon
            icon="sort"
            class="icon_sort"
            @click="toggleNumberSort"
          />

          <v-dropdown
            class="icon_filter"
            :triggers="[]"
            :autoHide="false"
            :shown="this.filterProp === 'postId'"
            top-start
          >
            <font-awesome-icon icon="filter" @click="openFilterTooltip" />

            <div slot="popper">
              <input
                class="popperInput"
                type="text"
                v-model="filterText"
                @input="searchText"
              />
              <font-awesome-icon
                icon="times"
                class="closeIcon"
                @click="closeFilterTooltip"
              />
            </div>
          </v-dropdown>
        </span>
        <span class="cell cell_email" id="email">
          <p>Email</p>
          <font-awesome-icon
            icon="sort"
            class="icon_sort"
            @click="toggleStringSort"
          />
          <v-dropdown
            class="icon_filter"
            :triggers="[]"
            :autoHide="false"
            :shown="this.filterProp === 'email'"
            top-start
          >
            <font-awesome-icon icon="filter" @click="openFilterTooltip" />

            <div slot="popper">
              <input
                class="popperInput"
                type="text"
                v-model="filterText"
                @input="searchText"
              />
              <font-awesome-icon
                icon="times"
                class="closeIcon"
                @click="closeFilterTooltip"
              />
            </div>
          </v-dropdown>
        </span>
        <span class="cell cell_name" id="name"
          ><p>Name</p>
          <font-awesome-icon
            icon="sort"
            class="icon_sort"
            @click="toggleStringSort" />

          <v-dropdown
            class="icon_filter"
            :triggers="[]"
            :autoHide="false"
            :shown="this.filterProp === 'name'"
          >
            <font-awesome-icon icon="filter" @click="openFilterTooltip" />

            <div slot="popper">
              <input
                class="popperInput"
                type="text"
                v-model="filterText"
                @input="searchText"
              />
              <font-awesome-icon
                icon="times"
                class="closeIcon"
                @click="closeFilterTooltip"
              />
            </div> </v-dropdown
        ></span>
      </div>
      <RecycleScroller
        :items="filterRows"
        :item-size="70"
        :buffer="20"
        page-mode
        key-field="id"
        v-slot="{ item }"
      >
        <div class="row row_body">
          <span class="cell cell_id"
            ><p>{{ item.id }}</p></span
          >
          <span class="cell cell_postId"
            ><p>{{ item.postId }}</p></span
          >
          <span class="cell cell_email"
            ><p>{{ item.email }}</p></span
          >
          <span class="cell cell_name"
            ><p>{{ item.name }}</p></span
          >
        </div>
      </RecycleScroller>
    </div>
  </div>
</template>

<script>
export default {
  name: 'virtual-scroll',

  props: {},
  data() {
    return {
      rows: [],
      sortDirection: '',
      filterText: '',
      filterColumn: '',
      filterProp: '',
    }
  },
  async created() {
    try {
      const res = await fetch(`https://jsonplaceholder.typicode.com/comments`)
      this.rows = await res.json()
    } catch (err) {
      console.error('Error', err)
    }
  },
  computed: {
    filterRows() {
      if (this.filterProp) {
        return this.rows.filter((row) => {
          return row[this.filterProp]
            .toString()
            .toLowerCase()
            .trim()
            .includes(this.filterText.toString().toLowerCase().trim())
        })
      } else {
        return this.rows
      }
    },
  },
  methods: {
    searchText(e) {
      this.filterText = e.target.value
    },
    toggleNumberSort(e) {
      this.filterColumn = e.target.closest('span').getAttribute('id')
      if (this.sortDirection === 'asc' || !this.sortDirection) {
        this.sortDirection = 'desc'
        this.rows = this.rows.sort(
          (a, b) => b[this.filterColumn] - a[this.filterColumn]
        )
      } else {
        this.sortDirection = 'asc'
        this.rows = this.rows.sort(
          (a, b) => a[this.filterColumn] - b[this.filterColumn]
        )
      }
    },
    toggleStringSort(e) {
      this.filterColumn = e.target.closest('span').getAttribute('id')
      if (this.sortDirection === 'desc' || !this.sortDirection) {
        this.sortDirection = 'asc'
        this.rows = this.rows.sort((a, b) =>
          a[this.filterColumn].localeCompare(b[this.filterColumn])
        )
      } else {
        this.sortDirection = 'desc'
        this.rows = this.rows.sort((a, b) =>
          b[this.filterColumn].localeCompare(a[this.filterColumn])
        )
      }
    },

    openFilterTooltip(e) {
      const filterColumn = e.target.closest('span').getAttribute('id')
      this.filterProp = filterColumn
    },

    closeFilterTooltip() {
      this.filterProp = ''
      this.filterText = ''
    },
  },
}
</script>
<style scoped>
.row {
  display: flex;
  width: 100%;
  border-top: 1px solid #c8cacc;
}
.cell {
  text-align: left;
  height: 100%;
  display: flex;
  align-items: center;
}

.cell p {
  margin: 1rem;
  margin-right: 6px;
}
.cell_id {
  width: 10%;

  margin-top: 0;
  margin-bottom: 0;
}
.cell_postId {
  width: 15%;
}
.cell_email {
  width: 30%;
}
.cell_name {
  width: 45%;
}
.row_header {
  background: #c7cbcb;
  font-weight: bold;
}
.row_body {
}

.icon_sort {
  margin-left: 2px;
  cursor: pointer;
}
.icon_filter {
  margin-left: auto;
  margin-right: 24px;
}

.icon_filter:hover {
  cursor: pointer;
}

.closeIcon {
  position: absolute;
  top: 6px;
  right: 6px;
}
.closeIcon:hover {
  cursor: pointer;
}
.popperInput {
  border: 1px solid rgb(182, 181, 181);
  border-radius: 5px;
  outline: none;
}
</style>
