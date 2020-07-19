<template>
  <div style>
    <blockquote style="background-color: #ac9;">
      <b>help</b>: add query terms and "phrases" to the search bar. if you
      prefix a term with one of
      <span style="font-family:monospace">'+', '?', '-',</span> that term will
      be counted as one of AND, OR, or NOT. You can click on the operator in the
      schematic window to change the operation. Hit enter or click "buildQuery"
      to generate valid AQL.
    </blockquote>
    <span style="display: flex; align-items: center;">
      <textarea
        v-model="query"
        ref="searchbar"
        id="searchbar"
        :rows="rows"
        wrap="hard"
        @keydown.enter.prevent="buildQuery"
        :autofocus="'autofocus'"
      />
      <span
        v-if="querying"
        class="loading dots2"
        style="display:inline-flex"
      ></span>
      <span v-else>
        <button @click="buildQuery">build AQL query</button>
        <!-- <button @click="saveQuery">
          <span>save</span>
        </button>-->
      </span>
    </span>
  </div>
</template>

<script>
import { mapState } from 'vuex'
import { buildAQL } from '@hp4k1h5/aqlquerybuilder.js'

export default {
  name: 'Searchbar',

  computed: {
    ...mapState({
      filters: state => state.search.filters,
      querying: state => state.search.querying,
    }),

    query: {
      get: function() {
        return this.$store.state.search.query
      },

      set: function(val) {
        this.$store.commit('search/SET_QUERY', val)
      },
    },

    genAQL: {
      get: function() {
        return this.$store.state.search.genAQL
      },
      set: function(val) {
        this.$store.state.search.genAQL = val
      },
    },
  },

  data() {
    return {
      rows: 1,
      rowHeight: 38,
    }
  },

  methods: {
    async buildQuery() {
      this.genAQL = buildAQL({
        view: 'view-name',
        collections: [{ name: 'col_name', analyzer: 'analyzer_name' }],
        terms: this.query,
        filters: this.filters,
      })
    },

    calculateInputHeight() {
      const input = this.$refs.searchbar
      if (!input) return

      input.style.height = '0'
      const height = input.scrollHeight
      const minHeight = this.rows * this.rowHeight
      input.style.height = Math.max(minHeight, height) + 'px'
    },
  },

  async created() {
    this.query = `these things +"and others"  -"but not more things"`
    this.calculateInputHeight()
    await this.$nextTick()
    this.$refs.searchbar.focus()
    this.buildQuery()
  },

  watch: {
    query() {
      this.calculateInputHeight()
    },
  },
}
</script>

<style scoped>
.select {
  display: inline-flex;
  max-height: 28px;
  margin-left: 8px;
  border: solid 1px black;
  border-radius: 8px;
  font-family: Monda;
  font-size: 16px;
  font-weight: bold;
}

textarea {
  width: 60%;
  min-height: 22px;
  margin: 4px;
  padding: 4px;
  resize: none;
  border-radius: 8px;
  box-sizing: border-box;
  font-size: 18px;
  font-weight: bold;
  font-family: Monda;
}
</style>
