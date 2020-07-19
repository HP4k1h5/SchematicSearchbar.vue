<template>
  <div class="schema-box" v-if="queryArr.length">
    <span v-for="(q, i) in queryArr" :key="i">
      <span
        @click="nextOp(i)"
        class="op"
        :style="{ backgroundColor: q.color }"
        >{{ q.op }}</span
      >
      <span v-if="bolds(q)" class="bold">{{ q.val[0] }}</span>
      <span class="text">{{ bolds(q) ? q.val.slice(1, -1) : q.val }}</span>
      <span v-if="bolds(q)" class="bold">{{ q.val.slice(-1) }}</span>
    </span>
  </div>
</template>

<script>
import { parseQuery } from '@hp4k1h5/aqlquerybuilder.js'

export default {
  computed: {
    query: {
      get: function() {
        return this.$store.state.search.query
      },
      set: function(val) {
        this.$store.state.search.query = val
      },
    },

    queryArr: {
      get: function() {
        return this.$store.state.search.queryArr
      },
      set: function(val) {
        this.$store.state.search.queryArr = val
      },
    },
  },

  methods: {
    /* rotates through boolean operators and updates query string And query
     * array
     * */
    nextOp(i) {
      /* get next operator */
      const ops = ['?', '+', '-']
      const opI = ops.findIndex(o => o === this.queryArr[i].op)
      const next = ops[(opI + 1) % ops.length]

      /* update query string with new operator*/
      const termRgx = new RegExp(
        '(?<=^|[' + this.queryArr[i].op + ' ])' + this.queryArr[i].val,
      )
      const termI = this.query.match(termRgx).index
      if (!termI || this.query[termI - 1] == ' ') {
        this.query = this.query.slice(0, termI) + next + this.query.slice(termI)
      } else {
        this.query =
          this.query.slice(0, termI - 1) + next + this.query.slice(termI)
      }

      /* update queryArr, happens through change detection anyway bc of the
       * watcher on query below, but leaving in case people want to see a more
       * declarative ex.
       * */
      this.queryArr[i].op = next
      this.queryArr[i].color = this.colorMap(next)
    },

    bolds(p) {
      return ['phr', 'prox'].includes(p.type)
    },

    colorMap(op) {
      const m = {
        '+': '#65be65',
        '-': '#fe3345',
        '?': '#23deb5',
      }
      return m[op]
    },
  },

  watch: {
    query: {
      immediate: true,
      handler: function() {
        this.queryArr = parseQuery(this.query).map(t => ({
          ...t,
          color: this.colorMap(t.op),
        }))
      },
    },
  },
}
</script>

<style scoped>
.schema-box {
  padding: 12px;
  border: solid 2px #555;
  font-size: 18px;
  font-weight: bold;
}
span {
  padding: 2px;
}
.op {
  margin-top: 15px;
  margin-bottom: 15px;
  margin-right: 5px;
  margin-left: 5px;
  padding-right: 4px;
  padding-left: 4px;
  border-radius: 15px;
  font-size: 16px;
  transform: scale(0.1, 6);
  font-weight: bolder;
  cursor: pointer;
}
.bold {
  font-size: 24px;
  font-weight: bold;
}
</style>
