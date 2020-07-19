<template>
  <div style="border-bottom: solid 1px black">
    <div class="actions">
      <span style="font-weight: bold; margin: 2px;">Filters</span>
      <button
        @click="
          filtersOn = true
          addFilter()
        "
      >
        <span>add</span>
      </button>
      <button @click="filters = []">
        <span>clear</span>
      </button>
      <button @click="filtersOn = !filtersOn">
        <span>{{ filtersOn ? 'close' : 'expand' }}</span>
      </button>
    </div>
    <div v-show="filtersOn" class="filters">
      <span
        v-for="(f, i) in filters"
        :key="i"
        class="filter"
        :style="{ backgroundColor: f.color }"
      >
        <button @click="editFilter(i)" class="btn">edit</button>
        <span id="field" class="field-val">{{ f.field }}</span>
        <span id="op" class="op">{{ f.op }}</span>
        <span id="val" class="field-val">{{ f.val }}</span>
        <button @click="filters.splice(i, 1)" class="btn">close</button>
      </span>
    </div>
    <div v-if="filterEdit" class="editor">
      <select v-model="filterEdit.field" id="field-select">
        <option v-for="val in ['author', 'title', 'text']" :key="val">
          {{ val }}
        </option>
      </select>
      <select v-model="filterEdit.op" id="op-select">
        <option v-for="val in ['LIKE', '>', '<', '==', '!=']" :key="val">
          {{ val }}
        </option>
      </select>
      <input
        v-model="filterEdit.val"
        @keyup.enter="reAddFilter"
        id="val-input"
      />
      <button @click="reAddFilter">
        <span>add</span>
      </button>
      <button @click="filterEdit = null">
        <span>clear</span>
      </button>
    </div>
  </div>
</template>

<script>
export default {
  computed: {
    filters: {
      get: function() {
        return this.$store.state.search.filters
      },
      set: function(val) {
        this.$store.state.search.filters = val
      },
    },

    subtitleStyles() {
      return {
        color: this.filters.length ? '#45ef89' : '333',
      }
    },
  },

  data() {
    return {
      filtersOn: true,
      filterEdit: null,
      colorMap: {
        LIKE: '#45ef89',
        '<': '#bc87aa',
        '>': '#34bcbc',
        '==': '#45ff45',
        '!=': '#ff4545',
      },
    }
  },

  methods: {
    addFilter() {
      this.filterEdit = {
        field: 'author',
        op: 'LIKE',
        val: '%Berens%',
        color: this.colorMap['LIKE'],
      }
    },

    editFilter(i) {
      this.filterEdit = this.filters.splice(i, 1)[0]
    },

    reAddFilter() {
      this.filterEdit.color = this.colorMap[this.filterEdit.op]
      this.filters.push(this.filterEdit)
      this.filterEdit = null
    },
  },

  created() {
    this.addFilter()
    !this.filters.length && this.reAddFilter()
  },
}
</script>

<style scoped>
.filters {
  width: 100%;
  min-height: 50px;
}
.filter {
  display: inline-flex;
  margin: 10px;
  padding: 1px;
  border: solid 1px grey;
  border-radius: 20px;
}
.field-val {
  margin: 7px;
  font-weight: bold;
}
.op {
  margin: 7px;
  background-color: #ccc;
  font-size: 12px;
  font-weight: bold;
}
#field-select {
  padding: 6px;
  font-size: 17px;
}
#op-select {
  padding: 6px;
  font-size: 17px;
}
#val-input {
  font-size: 18px;
}
.btn {
  border-radius: 10px;
  margin: 5px;
  padding: 0px;
  font-weight: bold;
}
</style>
