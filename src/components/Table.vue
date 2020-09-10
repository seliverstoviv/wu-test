<template>
  <div class="table">
    <div class="table__actions">
      <div class="table__text-search">
        <input type="text" v-model="textSearch" placeholder="Поиск...">
        {{ textSearch }}
      </div>
      <div class="table__pagination">
        <button
          class="btn btn__icon mr-1"
          @click="numPage = 1"
          :disabled="numPage === 1"
        >
            1
        </button>
        <button
          class="btn btn__icon mr-1"
          @click="numPage -= 1"
          :disabled="numPage === 1"
        >
          z
        </button>
        <span class="mr-1">{{ numPage }} из {{ totalPage }}</span>
        <button
          class="btn btn__icon mr-1"
          @click="numPage += 1"
          :disabled="numPage === totalPage"
        >
          ᐳ
        </button>
        <button
          class="btn btn__icon mr-1"
          @click="numPage = totalPage"
          :disabled="numPage === totalPage"
        >
            {{ totalPage }}
        </button>
      </div>
    </div>
    <table>
      <thead class="table__header">
        <tr class="table__row table__row__header">
          <th
            v-for="(item,index) in cols"
            :key="'colsName' + Math.random() + index"
            @click="setSort(item)"
          >
            <div class="header-cell">
              {{ item.title }}
              <div class="table__sort-btn">
                <span
                  :class="sortBy === item.property ? 'sort-icon-vis' : 'sort-icon'"
                  class="ml-1"
                  v-text="sortUp ? '🠕' : '🠗'">
                </span>
              </div>
            </div>
          </th>
        </tr>
      </thead>
      <tbody class="table__body">
        <tr class="table__row table__row__body"
            v-for="(item, index) in rowsC"
            :key="'rowc' + index"
            @click="$emit('click-row', item)"
        >
          <td
            v-for="(col,index) in cols"
            :key="'colsName2' + Math.random() + index"
          >
            {{ item[col.property] }}
          </td>
        </tr>
      </tbody>
    </table>
  </div>
</template>
<script>

export default {
  components: {
    // HeaderCell,
    // Row,
  },
  data() {
    return {
      sortUp: true,
      numPage: 1,
      numDisItems: 10,
      sortBy: '',
      textSearch: '',
    };
  },
  props: {
    rows: {
      type: Array,
      default: () => [],
    },
    cols: {
      type: Array,
      default: () => [],
    },
  },
  methods: {
    setSort(el) {
      this.sortBy = el.property;
      this.sortUp = !this.sortUp;
      this.numPage = 1;
    },
  },
  computed: {
    rowFilter() {
      let sortRow = this.rows.slice();
      if (this.textSearch) {
        const regPharse = new RegExp(this.textSearch.toLowerCase(), 'i');
        sortRow = sortRow.filter((el) => {
          const sumLine = this.cols.reduce((acc, value) => `${acc} ${el[value.property]}`, '').toLowerCase();
          return regPharse.test(sumLine);
        });
      }
      return sortRow;
    },
    rowsC() {
      const rowC = [];
      const sortRow = this.rowFilter.slice();
      if (sortRow.length > 0 && this.sortBy) {
        if (!this.sortUp) {
          sortRow.sort((a, b) => (a[this.sortBy] > b[this.sortBy] ? 1 : -1));
        } else {
          sortRow.sort((a, b) => (a[this.sortBy] < b[this.sortBy] ? 1 : -1));
        }
      }

      const limit = this.rowFilter.length < this.numDisItems
        ? this.rowFilter.length : this.fromWhatToWhat.end;

      for (let i = this.fromWhatToWhat.start; i < limit; i += 1) {
        rowC.push(sortRow[i]);
      }
      return rowC;
    },
    rowsFormat() {
      return this.rows;
    },
    totalPage() {
      return this.rowFilter && this.rowFilter.length > this.numDisItems
        ? Math.floor(this.rowFilter.length / this.numDisItems) : 1;
    },
    fromWhatToWhat() {
      const sObj = {};
      sObj.start = (this.numPage - 1) * this.numDisItems;
      sObj.end = sObj.start + this.numDisItems;
      return sObj;
    },
  },
};
</script>
