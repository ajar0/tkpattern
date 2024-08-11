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
      <div :class="'icon-action-' + action.do" :style="'align-self: ' + actionMap[action.do].align">
        {{ actionMap[action.do].sign }}
      </div>
    </div>
  </div>
</template>

<script>
const actionMap = {
  a: { align: 'center' },
  u: { align: 'flex-start' },
  d: { align: 'flex-end' },
  x: { align: 'center' }
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
.action-cards {
  display: flex;
  align-items: center;
}
.action-card {
  display: flex;
  justify-content: center;
  width: 2rem;
  height: 3rem;
  border: 2px var(--color-border) solid;
  cursor: pointer;
}
</style>
