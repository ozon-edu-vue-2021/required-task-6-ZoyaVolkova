<script lang="jsx">
import { orderBy } from 'lodash/collection'
import FilterDropdown from './filter-dropdown'
import TablePaginator from './table-paginator'
import DotsLoaderIcon from './dost-loader.svg'

export default {
  name: 'table-component',
  props: {
    rows: {
      type: Array,
      default: () => [],
    },
    totalPages: {
      type: Number,
      default: 0,
    },
    currentPage: {
      type: Number,
      default: 0,
    },
    staticPaging: {
      type: Boolean,
      default: true,
    },
    pageRows: {
      type: Array,
      default: () => [],
    },
  },
  data() {
    return {
      sortProp: '',
      sortDirection: '',
      filterProp: '',
      filterText: '',
    }
  },
  computed: {
    sortedRows() {
      let from = (this.currentPage - 1) * 10
      let to = from + 10

      if (!this.staticPaging) {
        to = this.currentPage * 10 + 1
        from = 0
      }

      if (this.sortProp) {
        return orderBy(this.rows, [this.sortProp], [this.sortDirection]).slice(
          from,
          to
        )
      } else if (this.filterText) {
        if (this.filterProp === 'postId' || this.filterProp === 'id') {
          return this.getFilterNumber(from, to)
        } else {
          return this.getFilterText(from, to)
        }
      } else {
        return this.pageRows
      }
    },
  },
  methods: {
    getFilterNumber(from, to) {
      return this.rows
        .filter((row) =>
          row[this.filterProp]
            .toString()
            .trim()
            .includes(this.filterText.toString().trim())
        )
        .slice(from, to)
    },
    getFilterText(from, to) {
      return this.rows
        .filter((row) =>
          row[this.filterProp]
            .toLowerCase()
            .trim()
            .includes(this.filterText.toLowerCase().trim())
        )
        .slice(from, to)
    },

    toggleSort(prop) {
      const { getPage } = this.$listeners
      this.sortProp = prop
      this.sortDirection =
        this.sortDirection === 'desc' || !this.sortDirection ? 'asc' : 'desc'
      getPage(1)
    },
    openFilterTooltip(prop = '') {
      const { getPage } = this.$listeners
      if (this.staticPaging) {
        getPage(1)
      }

      this.filterProp = prop
      this.filterText = ''
    },
    setFilterText(e) {
      this.sortProp = ''
      this.filterText = e.target.value
    },
    renderHead(h, columnsOptions) {
      const { $style, sortProp, sortDirection, filterProp, filterText } = this

      return columnsOptions.map((column) => {
        const renderedTitle = column.scopedSlots.title
          ? column.scopedSlots.title()
          : column.title
        let sortIcon = 'sort'

        if (sortProp === column.prop) {
          sortIcon =
            sortDirection === 'asc' ? 'sort-amount-down' : 'sort-amount-up'
        }

        return (
          <th key={column.prop} class={$style.headerCell}>
            <div class={$style.headerCellContent}>
              <span>{renderedTitle}</span>
              <font-awesome-icon
                class={$style.sortIcon}
                icon={sortIcon}
                on={{ click: () => this.toggleSort(column.prop) }}
              />
              <FilterDropdown
                columnProp={column.prop}
                shown={column.prop === filterProp}
                filterText={filterText}
                on={{
                  openFilterTooltip: () => this.openFilterTooltip(column.prop),
                  closeFilterTooltip: () => this.openFilterTooltip(),
                  setFilterText: this.setFilterText,
                }}
              />
            </div>
          </th>
        )
      })
    },
    renderRows(h, columnsOptions) {
      return this.sortedRows.map((row, index) => {
        return (
          <tr key={index}>{...this.renderColumns(h, row, columnsOptions)}</tr>
        )
      })
    },
    renderColumns(h, row, columnsOptions) {
      return columnsOptions.map((column) => {
        return (
          <td key={column.prop} class={[this.$style.cell, column.prop]}>
            {column.scopedSlots.body
              ? column.scopedSlots.body({ row })
              : row[column.prop]}
          </td>
        )
      })
    },
    getColumnOptions() {
      return this.$slots.default
        .filter(
          (item) =>
            item.componentOptions && item.componentOptions.tag === 'TableColumn'
        )
        .map((column) =>
          Object.assign({}, column.componentOptions.propsData, {
            scopedSlots: column.data.scopedSlots || {},
          })
        )
    },
    renderInfPager() {
      const directives = [
        {
          name: 'detect-viewport',
          value: {
            callback: this.$listeners.infGetPage,
          },
        },
      ]

      const style = {
        background: `url("${DotsLoaderIcon}") no-repeat center`,
      }
      if (this.currentPage <= this.totalPages) {
        return <div {...{ class: this.$style.infPager, style, directives }} />
      }
    },
  },

  render(h) {
    const { $style, totalPages, currentPage, staticPaging, $listeners } = this
    const { getPage } = $listeners
    const columnsOptions = this.getColumnOptions()
    const columnsHead = this.renderHead(h, columnsOptions)
    const rows = this.renderRows(h, columnsOptions)

    return (
      <div>
        <table class={$style.table}>
          <thead>{...columnsHead}</thead>
          <tbody>{...rows}</tbody>
        </table>

        {staticPaging ? (
          <TablePaginator
            totalPages={totalPages}
            currentPage={currentPage}
            on={{ getPage: getPage }}
          />
        ) : (
          this.renderInfPager()
        )}
      </div>
    )
  },
}
</script>

<style module>
.table {
  border-spacing: 0;
  margin-top: 20px;
  width: 100%;
}

.cell {
  text-align: left;
  border-bottom: 1px solid #c8cacc;
  padding: 1rem 1rem;
}
.cell:first-child {
  width: 10%;
}
.cell:nth-child(2) {
  width: 15%;
}
.cell:nth-child(3) {
  width: 30%;
}
.cell:nth-child(4) {
  width: 45%;
}

.headerCell {
  composes: cell;
  background: #c7cbcb;
}

.infPager {
  width: 100%;
  height: 32px;
}
.headerCellContent {
  display: flex;
  align-items: center;
}

.sortIcon {
  margin-left: 8px;
}

.sortIcon:hover {
  cursor: pointer;
}
</style>
