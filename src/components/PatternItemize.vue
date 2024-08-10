<template>
  <div class="patterns-page">
    <div
      ref="patterntext"
      class="editbox"
      contenteditable
      @input="updatePatternText"
      @paste="trimPastedText"
    ></div>
    <hr />
    <table>
      <thead>
        <tr>
          <th>Turn</th>
          <th>Name</th>
          <th>Description</th>
          <th>Memo</th>
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
          <td>
            <div contenteditable></div>
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
      this.patternText = this.$refs.patterntext.innerText
      const lines = this.patternText.split('\n')
      this.patterns = lines
        .map((line) => {
          const regex = /^((\d+)n)?\s*\+?\s*(\d*)\s*(.*)\s*:\s*(.*)$/
          const matches = line.match(regex)
          if (matches) {
            const interval = parseInt(matches[2])
            const start = matches[3] ? parseInt(matches[3]) : 0
            const name = matches[4]
            const description = matches[5]
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
    },
    trimPastedText(event) {
      const html = (event.clipboardData || window.clipboardData).getData('text/html')
      const patternTextDiv = this.$refs.patterntext
      patternTextDiv.innerHTML = html
      const trimmedText = patternTextDiv.innerText.replace(/^\s*[\r\n]/gm, '')
      patternTextDiv.innerText = trimmedText
      event.preventDefault()
      this.updatePatternText()
    },
    resizeTextarea() {
      const lines = this.patternText.split('\n').length
      const textarea = this.$refs.patterntext
      textarea.style.height = `${lines}rm`
    },
    updatePatternText() {
      this.createPatterns()
      this.applyPatterns()
      this.resizeTextarea()
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

.patterns-page .editbox {
  width: 100%;
  background-color: var(--color-background-soft);
}

.patterns-page table,
tr,
td,
th {
  border: 1px solid var(--color-border);
}
</style>
