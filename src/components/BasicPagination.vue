<template>
  <nav>
    <ul class="pagination justify-content-center">
      <li
        v-if="!isFirstPageSelected()"
        :class="['page-item', isFirstPageSelected() ? 'disabled' : '']"
      >
        <a
          class="page-link"
          aria-label="Previous"
          @click="prevPage()"
          @keyup.enter="prevPage()"
          :tabindex="isFirstPageSelected() ? -1 : 0"
        >
          <span aria-hidden="true">&laquo;</span>
          <span class="sr-only">Previous</span>
        </a>
      </li>

      <li
        v-for="(page, index) in pages"
        :key="index"
        :class="['page-item', page.selected ? 'active' : '', page.disabled ? 'disabled' : '']"
      >
        <a class="page-link" v-if="page.breakView" tabindex="0">
          <slot name="breakViewContent">...</slot>
        </a>
        <a v-else-if="page.disabled" class="page-link" tabindex="0">{{ page.content }}</a>
        <a
          v-else
          class="page-link"
          @click="onPageSelected(page.index + 1)"
          @keyup.enter="onPageSelected(page.index + 1)"
          tabindex="0"
        >{{ page.content }}</a>
      </li>

      <li
        v-if="!isLastPageSelected()"
        :class="['page-item', isLastPageSelected() ? 'disabled' : '']"
      >
        <a
          class="page-link"
          @click="nextPage()"
          @keyup.enter="nextPage()"
          :tabindex="isLastPageSelected() ? -1 : 0"
        >
          <span aria-hidden="true">&raquo;</span>
          <span class="sr-only">Next</span>
        </a>
      </li>
    </ul>
  </nav>
</template>

<script>
export default {
  props: {
    value: {
      type: Number
    },
    pageCount: {
      type: Number,
      required: true
    },
    selectedPage: {
      type: Number
    },
    onClickHandler: {
      type: Function,
      default: () => {}
    },
    marginPages: {
      type: Number,
      default: 1
    }
  },
  data() {
    return {
      pageNumber: 1
    }
  },
  beforeUpdate() {
    if (
      this.selectedPage !== undefined &&
      this.selectedPage !== this.selected
    ) {
      this.onPageSelected(this.selectedPage)
    }
  },
  computed: {
    pages: function() {
      let items = {}
      const PAGE_RANGE = 3
      const MARGIN_PAGES = 1

      let addPageItem = index => {
        let page = {
          index: index,
          content: index + 1,
          selected: index === this.selected - 1
        }

        items[index] = page
      }

      let addBreakView = index => {
        let breakView = {
          disabled: true,
          breakView: true
        }

        items[index] = breakView
      }

      if (this.pageCount <= PAGE_RANGE) {
        for (let index = 0; index < this.pageCount; index++) {
          addPageItem(index)
        }
      } else {
        const HALF_PAGE_RANGE = Math.floor(PAGE_RANGE / 2)

        for (let i = 0; i < MARGIN_PAGES; i++) {
          addPageItem(i)
        }

        let rangeMin = 0
        if (this.selected - HALF_PAGE_RANGE > 0) {
          rangeMin = this.selected - 1 - HALF_PAGE_RANGE
        }

        let rangeMax = rangeMin + PAGE_RANGE - 1
        if (rangeMax >= this.pageCount) {
          rangeMax = this.pageCount - 1
          rangeMin = rangeMax - PAGE_RANGE + 1
        }

        for (let i = rangeMin; i <= rangeMax && i <= this.pageCount - 1; i++) {
          addPageItem(i)
        }

        if (rangeMin > MARGIN_PAGES) {
          addBreakView(rangeMin - 1)
        }

        if (rangeMax + 1 < this.pageCount - MARGIN_PAGES) {
          addBreakView(rangeMax + 1)
        }

        for (
          let i = this.pageCount - 1;
          i >= this.pageCount - MARGIN_PAGES;
          i--
        ) {
          addPageItem(i)
        }
      }
      return items
    },
    selected: {
      get: function() {
        return this.value || this.pageNumber
      },
      set: function(newValue) {
        this.pageNumber = newValue
      }
    }
  },
  methods: {
    isFirstPageSelected() {
      return 1 === this.selected
    },
    isLastPageSelected() {
      return 0 === this.pageCount || this.pageCount === this.selected
    },
    prevPage() {
      if (this.selected > 1) {
        this.onPageSelected(this.selected - 1)
      }
    },
    nextPage() {
      if (this.selected < this.pageCount) {
        this.onPageSelected(this.selected + 1)
      }
    },
    onPageSelected(selected) {
      if (this.selected !== selected) {
        this.pageNumber = selected
        this.$emit('input', selected)
        this.onClickHandler(selected)
      }
    }
  }
}
</script>
<style scoped>
.pagination > li > a
{
    background-color: white;
    color: black;
}

.pagination > li > a:focus,
.pagination > li > a:hover,
.pagination > li > span:focus,
.pagination > li > span:hover
{
    color: white;
    background-color: #eee;
    border-color: #ddd;
}

.pagination > .active > a
{
    color: white;
    background-color: black !Important;
    border: solid 1px black !Important;
}

.pagination > .active > a:hover
{
    background-color: black !Important;
    border: solid 1px black;
}
</style>
