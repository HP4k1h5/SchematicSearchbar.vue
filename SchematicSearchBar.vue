<template>
  <div class="search-box">
    <Searchbar :queryString="queryString" :querying="querying"  @query="this.$emit('query')"/>
    <SchematicQuery :queryArray="queryArray"/>
    <Filters :filterArray="filterArray"/>
  </div>
</template>

<script>
import Searchbar from '@/components/search/Searchbar'
import SchematicQuery from '@/components/search/SchematicQuery'
import Filters from '@/components/search/Filters'

export default {
  name: 'SchematicSearchBar',

  props: ['queryString', 'queryArr', 'filterArr', 'querying'],

  components: {
    Searchbar,
    SchematicQuery,
    Filters,
  },

  methods:{
    colorMap(op) {
      const m = {
        '+': '#65be65',
        '-': '#fe3345',
        '?': '#23deb5',
      }
      return m[op]
    },
  },
}
</script>

<style scoped src="../style/index.css">
.code-block {
  display: flex;
  justify-content: space-around;
}
.block {
  width: 50%;
}
.block-label {
  margin: 0px 40px;
}
pre {
  max-width: 100%;
  overflow: scroll;
  border: 1px solid #ded;
  background-color: #282a36;
  color: #eef;
}
.clip {
  position: relative;
  top: 2px;
  left: 80%;
  font-size: 18px;
}
</style>
