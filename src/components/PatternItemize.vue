<script setup>
import ActionCards from './ActionCards.vue'
</script>

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
          <th class="actions">
            Action
            <button class="icon-delete" @click="actionCards = emptyActions()"></button>
          </th>
          <th>Memo <button class="icon-delete" @click="memos = emptyMemo()"></button></th>
        </tr>
      </thead>
      <tbody>
        <tr
          v-for="turn in turns"
          :key="turn.index"
          :class="{ active: turn.index === activeTurn }"
          @click="activateTurn(turn)"
        >
          <td>{{ turn.index }}</td>
          <td>
            <div v-for="name in turn.names" :key="name">{{ name }}</div>
          </td>
          <td>
            <div v-for="description in turn.descriptions" :key="description">{{ description }}</div>
          </td>
          <td class="flex">
            <div>
              <ActionCards
                :actions="actionCards[turn.index - 1]"
                :isActive="turn.index === activeTurn"
              />
            </div>
            <div>
              <button class="icon-copy" @click="copyActions(turn)"></button>
              <button
                class="icon-paste"
                @click="pasteActions(turn)"
                :disabled="!clipboard"
              ></button>
            </div>
          </td>
          <td>
            <input class="memo" v-model="memos[turn.index - 1]" />
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
      turns: this.emptyTurns(),
      actionCards: this.emptyActions(),
      memos: this.emptyMemo(),
      clipboard: null,
      activeTurn: -1
    }
  },
  methods: {
    emptyTurns() {
      return Array.from({ length: maxTurn }, (_, index) => ({
        index: index + 1,
        names: [],
        descriptions: []
      }))
    },
    emptyActions() {
      return Array.from({ length: maxTurn }, () =>
        Array.from({ length: 5 }, (_, i) => ({ position: i, seq: i, do: 'a' }))
      )
    },
    emptyMemo() {
      return Array.from({ length: maxTurn }, () => '')
    },
    copyActions(turn) {
      this.clipboard = this.actionCards[turn.index - 1]
    },
    pasteActions(turn) {
      if (!this.clipboard) return
      this.actionCards[turn.index - 1] = JSON.parse(JSON.stringify(this.clipboard))
    },
    activateTurn(turn) {
      this.activeTurn = turn.index
    },
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
      this.turns = this.emptyTurns()

      this.patterns.forEach((pattern) => {
        let turn = pattern.start ? pattern.start : pattern.interval
        while (turn <= maxTurn) {
          this.turns[turn - 1].names.push(pattern.name)
          this.turns[turn - 1].descriptions.push(pattern.description)
          turn += pattern.interval
        }
      })
    },
    trimPastedText(event) {
      const clipboardData = event.clipboardData || window.clipboardData
      const html = clipboardData.getData('text/html')
      const patternTextDiv = this.$refs.patterntext
      if (html) {
        patternTextDiv.innerHTML = html
        patternTextDiv.innerText = patternTextDiv.innerText.replace(/^\s*[\r\n]/gm, '')
      } else {
        patternTextDiv.innerText = clipboardData.getData('text/plain')
      }
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
.patterns-page table tr.active {
  background-color: var(--color-background-soft);
}
.patterns-page .memo {
  width: 100%;
}

.patterns-page .flex {
  display: flex;
  align-items: center;
}

.patterns-page .actions {
  width: 14rem;
}
</style>
