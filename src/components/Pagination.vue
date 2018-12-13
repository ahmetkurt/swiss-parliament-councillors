<template>
  <ul class="pagination">
    <li class="pagination__item">
      <button class="pagination__button" type="button" @click="onClickFirstPage"
        :disabled="isInFirstPage">
        First
      </button>
    </li>
    <li class="pagination__item">
      <button class="pagination__button" type="button" @click="onClickPreviousPage"
        :disabled="isInFirstPage">
        Previous
      </button>
    </li>
    <li class="pagination__item" v-for="(item, index) in pages" :key="index">
      <button class="pagination__button pagination__button--number"
        :class="{ 'pagination__button--active': isPageActive(item.name) }" type="button"
        @click="onClickPage(item.name)" :disabled="item.isDisabled">
        {{ item.name }}
      </button>
    </li>
    <li class="pagination__item">
      <button class="pagination__button" type="button" @click="onClickNextPage"
        :disabled="isInLastPage">
        Next
      </button>
    </li>
    <li class="pagination__item">
      <button class="pagination__button" type="button" @click="onClickLastPage"
        :disabled="isInLastPage">
        Last
      </button>
    </li>
  </ul>
</template>

<script>
export default {
  name: 'Pagination',
  props: {
    total: {
      type: Number,
      required: true,
    },
    totalPages: {
      type: Number,
      required: true,
    },
    currentPage: {
      type: Number,
      required: true,
    },
    maxVisibleButtons: {
      type: Number,
      required: false,
      default: 3,
    },
  },
  computed: {
    startPage() {
      const vm = this;
      const ceiledTotalPages = Math.ceil(vm.totalPages);

      if (vm.currentPage === 1) return 1;

      if (vm.currentPage === ceiledTotalPages && ceiledTotalPages >= vm.maxVisibleButtons) {
        return ceiledTotalPages - vm.maxVisibleButtons + 1;
      }

      return vm.currentPage - 1;
    },
    endPage() {
      const vm = this;

      return Math.min(vm.startPage + vm.maxVisibleButtons - 1, Math.ceil(vm.totalPages));
    },
    pages() {
      const vm = this;
      const range = [];

      for (let i = vm.startPage; i <= vm.endPage; i += 1) {
        range.push({
          name: i,
          isDisabled: i === vm.currentPage,
        });
      }

      return range;
    },
    isInFirstPage() {
      const vm = this;

      return vm.currentPage === 1;
    },
    isInLastPage() {
      const vm = this;

      return vm.currentPage >= Math.ceil(vm.totalPages);
    },
  },
  methods: {
    onClickFirstPage() {
      const vm = this;

      vm.$emit('pagechanged', 1);
    },
    onClickPreviousPage() {
      const vm = this;

      vm.$emit('pagechanged', vm.currentPage - 1);
    },
    onClickPage(page) {
      const vm = this;

      vm.$emit('pagechanged', page);
    },
    onClickNextPage() {
      const vm = this;

      vm.$emit('pagechanged', vm.currentPage + 1);
    },
    onClickLastPage() {
      const vm = this;

      vm.$emit('pagechanged', Math.ceil(vm.totalPages));
    },
    isPageActive(page) {
      const vm = this;

      return vm.currentPage === page;
    },
  },
};
</script>

<style lang="scss" scoped>
.pagination {
  display: flex;
  padding-left: 0;
  list-style: none;

  $this: &;

  &__item {
    display: block;
  }

  &__button {
    border: 0;
    background: none;
    font-size: .75rem;
    font-style: italic;
    text-decoration: underline;
    cursor: pointer;

    &:focus {
      outline: 1px solid #00c8ff;
      box-shadow: 0 0 5px #00c8ff;
    }

    &:disabled:not(&--active) {
      color: #999;
      text-decoration: none;
      cursor: not-allowed;
    }

    &--number {
      font-style: normal;
      text-decoration: none;
    }

    &--active {
      font-weight: 700;
      cursor: not-allowed;
    }
  }
}
</style>
