<script setup>
const actions = defineModel({ required: true })
</script>

<template>
  <div class="action-cards">
    <div
      v-for="action in actions"
      :key="action.position"
      class="action-card"
      @click="cycleActions(action)"
    >
      <div class="action-item" :style="actionMap[action.do].style">
        {{ actionMap[action.do].sign }}
      </div>
    </div>
  </div>
</template>

<script>
const actionMap = {
  a: { sign: '⇧', style: 'align-self: center' },
  u: { sign: '⇮', style: 'align-self: flex-start' },
  d: { sign: '⇩', style: 'align-self: flex-end' },
  '': { sign: ' ' }
}

export default {
  methods: {
    cycleActions(action) {
      const keys = Object.keys(actionMap)
      const currentIndex = keys.indexOf(action.do)
      const nextIndex = (currentIndex + 1) % keys.length
      action.do = keys[nextIndex]
    }
  }
}
</script>

<style scoped>
.action-cards,
.action-card {
  display: flex;
  justify-content: center;
}
.action-card {
  width: 2rem;
  height: 3rem;
  text-align: center;
  border: 2px var(--color-border) solid;
  cursor: pointer;
}
</style>
