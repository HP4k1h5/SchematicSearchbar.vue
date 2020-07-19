<template>
  <div >
    <span class="search-bar">
      <textarea
        v-model="queryString"
        ref="searchbar"
        id="searchbar"
        :rows="rows"
        wrap="hard"
        @keydown.enter.prevent="query"
        :autofocus="'autofocus'"
      />
    <!-- use <slot></slot> instead for each span/button --> 
      <span
        v-if="querying"
        class="querying dots2"
      ></span>
      <span v-else>
        <button @click="query">query</button>
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
  props: [ 'queryString', 'querying' ],

  data() {
    return {
      rows: 1,
      rowHeight: 38,
    }
  },

  methods: {
    query() {
      this.$emit('query')
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
  },

  watch: {
    query() {
      this.calculateInputHeight()
    },
  },
}
</script>

<style scoped src="../style/index.css">
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
