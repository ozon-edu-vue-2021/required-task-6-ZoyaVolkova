<template>
  <div class="container">
    <div class="buttons">
      <button
        type="button"
        class="button static_pages_button"
        :class="{ active: isStatic }"
        @click="getStatic"
      >
        Pages
      </button>
      <button
        type="button"
        class="button infinite_scroll_button"
        :class="{ active: isInfinite }"
        @click="getInfinite"
      >
        Infinite Scroll
      </button>
      <button
        type="button"
        class="button virtual_scroll_button"
        :class="{ active: isVirtual }"
        @click="getVirtual"
      >
        Virtual Scroll
      </button>
    </div>

    <div v-if="isStatic || isInfinite">
      <TableComponent
        :rows="rows"
        :total-pages="totalPages"
        :current-page="currentPage"
        :static-paging="isStatic"
        @getPage="getPage"
        @infGetPage="infGetPage"
        :pageRows="pageRows"
        class="table"
      >
        <TableColumn prop="id" title="ID" />
        <TableColumn prop="postId" title="Post ID" />

        <TableColumn prop="email">
          <template #title>
            <b>Email</b>
          </template>

          <template #body="{ row }">
            <a class="mail" :href="`mailto:${row.email}`">{{ row.email }}</a>
          </template>
        </TableColumn>

        <TableColumn prop="name" title="Name" />
      </TableComponent>
    </div>

    <div v-if="isVirtual">
      <VirtualScroll> </VirtualScroll>
    </div>
  </div>
</template>

<script>
import TableComponent from './table-component'
import TableColumn from './table-column'
import VirtualScroll from './virtual-scroll.vue'

export default {
  name: 'TableWrapper',
  components: {
    TableColumn,
    TableComponent,
    VirtualScroll,
  },
  data() {
    return {
      rows: [],
      currentPage: 1,
      isStatic: true,
      isVirtual: false,
      isInfinite: false,
      totalPages: 0,
      pageRows: [],
    }
  },
  created() {
    this.getAll()
  },

  methods: {
    async getAll() {
      try {
        const res = await fetch(`https://jsonplaceholder.typicode.com/comments`)
        this.rows = await res.json()
      } catch (err) {
        console.error('Error', err)
      }
      this.totalPages = Math.ceil(this.rows.length / 10)
      this.getPage(1)
    },
    getPage(number) {
      this.currentPage = number
      let from = (this.currentPage - 1) * 10
      let to = from + 10
      this.pageRows = this.rows.slice(from, to)
    },
    async infGetPage() {
      let to = this.currentPage * 10 + 1
      this.pageRows = this.rows.slice(0, to)
      this.currentPage++
    },
    getInfinite() {
      this.getPage(2)
      this.isStatic = false
      this.isVirtual = false
      this.isInfinite = true
    },
    getStatic() {
      this.isInfinite = false
      this.isVirtual = false
      this.isStatic = true
      this.getPage(1)
    },
    getVirtual() {
      this.isInfinite = false
      this.isStatic = false
      this.isVirtual = true
    },
  },
}
</script>
<style scoped>
.buttons {
  display: flex;
  margin-bottom: 50px;
}
.button {
  width: 100px;
  margin-right: 20px;
  padding: 10px;
  cursor: pointer;
  border: none;
  border-radius: 5px;
  background-color: rgb(175, 171, 171);
  color: #fff;
}
.button:hover {
  background-color: rgb(26, 120, 209);
}
.active {
  background-color: rgb(26, 120, 209);
}
.mail {
  text-decoration: none;
  color: inherit;
}
</style>
