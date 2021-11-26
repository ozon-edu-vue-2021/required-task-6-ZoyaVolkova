<script lang="jsx">
export default {
  name: 'table-paginator',
  props: {
    totalPages: {
      type: Number,
      default: 0,
    },
    currentPage: {
      type: Number,
      default: 0,
    },
  },
  computed: {
    shownPagesNumbers() {
      const { currentPage, totalPages } = this

      if (currentPage <= 3) {
        if (totalPages < 5) {
          return Array(totalPages)
            .fill(null)
            .map((index) => index + 1)
        }

        return [1, 2, 3, 4, 5]
      }

      if (currentPage >= totalPages - 2) {
        return [
          totalPages - 4,
          totalPages - 3,
          totalPages - 2,
          totalPages - 1,
          totalPages,
        ]
      }

      return [
        currentPage - 2,
        currentPage - 1,
        currentPage,
        currentPage + 1,
        currentPage + 2,
      ]
    },
  },
  render() {
    const { $style, shownPagesNumbers, currentPage, totalPages, $listeners } =
      this
    const { getPage } = $listeners

    return (
      <div class={$style.pagination}>
        <span class={$style.control} on={{ click: () => getPage(1) }}>
          <svg
            xmlns="http://www.w3.org/2000/svg"
            width="1.2em"
            viewBox="0 0 24 24"
          >
            <path
              d="M18.41 16.59L13.82 12l4.59-4.59L17 6l-6 6l6 6zM6 6h2v12H6z"
              fill="#afafaf"
            />
          </svg>
        </span>
        <span
          class={$style.control}
          on={{
            click: () =>
              currentPage !== 1 ? getPage(currentPage - 1) : getPage(1),
          }}
        >
          <svg
            xmlns="http://www.w3.org/2000/svg"
            width="20px"
            viewBox="0 0 24 24"
          >
            <g transform="rotate(180 12 12)">
              <path
                d="M10.02 6L8.61 7.41L13.19 12l-4.58 4.59L10.02 18l6-6l-6-6z"
                fill="#afafaf"
              />
            </g>
          </svg>
        </span>
        {...shownPagesNumbers.map((number) => (
          <span
            class={[
              $style.control,
              { [$style.active]: currentPage === number },
            ]}
            on={{ click: () => getPage(number) }}
          >
            {number}
          </span>
        ))}
        <span
          class={$style.control}
          on={{
            click: () =>
              currentPage !== totalPages
                ? getPage(currentPage + 1)
                : getPage(totalPages),
          }}
        >
          <svg
            xmlns="http://www.w3.org/2000/svg"
            width="20px"
            viewBox="0 0 24 24"
          >
            <path
              d="M10.02 6L8.61 7.41L13.19 12l-4.58 4.59L10.02 18l6-6l-6-6z"
              fill="#afafaf"
            />
          </svg>
        </span>
        <span class={$style.control} on={{ click: () => getPage(totalPages) }}>
          <svg
            xmlns="http://www.w3.org/2000/svg"
            width="20px"
            viewBox="0 0 24 24"
          >
            <path
              d="M6.29 8.11L10.18 12l-3.89 3.89A.996.996 0 1 0 7.7 17.3l4.59-4.59a.996.996 0 0 0 0-1.41L7.7 6.7a.996.996 0 0 0-1.41 0c-.38.39-.38 1.03 0 1.41zM17 6c.55 0 1 .45 1 1v10c0 .55-.45 1-1 1s-1-.45-1-1V7c0-.55.45-1 1-1z"
              fill="#afafaf"
            />
          </svg>
        </span>
      </div>
    )
  },
}
</script>

<style module>
.pagination {
  height: 48px;
  width: 100%;
  border-bottom: 1px solid #c8cacc;
  display: flex;
  align-items: center;
  margin-left: 8px;
}
.control {
  width: 24px;
  height: 20px;
  margin-right: 8px;
  padding: 4px;
}
.control:last-of-type {
  margin-right: 0;
}
.control:hover {
  cursor: pointer;
}
.control.active {
  color: rgb(26, 120, 209);
  border: 1px solid #c8cacc;
  border-radius: 6px;
}
</style>
