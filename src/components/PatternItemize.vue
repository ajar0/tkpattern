<template>
  <div class="patterns-page">
    <textarea v-model="patternText"></textarea>
    <hr />
    <table>
      <thead>
        <tr>
          <th>Turn</th>
          <th>Name</th>
          <th>Description</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="turn in turns" :key="turn[0]">
          <td>{{ turn[0] }}</td>
          <td>
            <div v-for="name in turn[1]" :key="name">{{ name }}</div>
          </td>
          <td>
            <div v-for="description in turn[2]" :key="description">{{ description }}</div>
          </td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script>
const maxTurn = 50

export default {
  data() {
    return {
      patternText: '',
      patterns: [],
      turns: Array.from({ length: maxTurn }, (_, index) => [index + 1, [], []])
    }
  },
  methods: {
    createPatterns() {
      const lines = this.patternText.split('\n')
      this.patterns = lines
        .map((line) => {
          const regex = /^(\d+)n\s*\+?\s*(\d*)\s*(.*)\s*:\s*(.*)$/
          const matches = line.match(regex)
          if (matches) {
            console.log(matches)
            const interval = parseInt(matches[1])
            const start = matches[2] ? parseInt(matches[2]) : 0
            const name = matches[3]
            const description = matches[4]
            return { start, interval, name, description }
          } else {
            // Handle invalid line format
            return null
          }
        })
        .filter((pattern) => pattern !== null)
    },
    applyPatterns() {
      this.turns.forEach((turn) => {
        turn[1] = []
        turn[2] = []
      })

      this.patterns.forEach((pattern) => {
        let turn = pattern.start ? pattern.start : pattern.interval
        while (turn <= maxTurn) {
          this.turns[turn - 1][1].push(pattern.name)
          this.turns[turn - 1][2].push(pattern.description)
          turn += pattern.interval
        }
      })
    }
  },
  watch: {
    patternText: {
      handler() {
        this.createPatterns()
        this.applyPatterns()
      }
    }
  }
}
</script>

<style scoped>
.patterns-page {
  padding: 2rem;
  min-height: 100vh;
}

.patterns-page table {
  width: 100%;
}

.patterns-page table,
tr,
td,
th {
  border: 1px solid var(--color-border);
}
.patterns-page textarea {
  width: 100%;
}
</style>
