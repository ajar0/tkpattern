<script setup>
const actions = defineModel('actions', { required: true })
const isActive = defineModel('isActive', { type: Boolean, required: true })
const gap = 0.2
</script>

<template>
  <div class="action-cards">
    <div class="cardslot">
      <div v-for="action in actions" :key="action.position">
        <button
          class="icon-up"
          v-if="isActive"
          :disabled="action.seq === 0"
          @click="adjustSeq(action, true)"
        ></button>
        <div
          class="action-card"
          :style="[
            'margin-top:' + action.seq * gap * (isActive ? 3 : 1) + 'rem',
            'margin-bottom:' + (4 - action.seq) * gap * (isActive ? 3 : 1) + 'rem'
          ]"
          @click="cycleActions(action)"
        >
          <div :class="['icon-action-' + action.do, 'act-' + action.do]"></div>
        </div>
        <button
          class="icon-down"
          v-if="isActive"
          :disabled="action.seq === 4"
          @click="adjustSeq(action, false)"
        ></button>
      </div>
    </div>
  </div>
</template>

<script>
const actionList = ['a', 'u', 'd', 'x']

export default {
  methods: {
    cycleActions(action) {
      const currentIndex = actionList.indexOf(action.do)
      const nextIndex = (currentIndex + 1) % actionList.length
      action.do = actionList[nextIndex]
    },
    adjustSeq(action, isUp) {
      const amount = isUp ? -1 : 1
      this.actions
        .filter((a) => a.seq == action.seq + amount)
        .forEach((action) => {
          action.seq -= amount
        })
      action.seq += amount
    }
  }
}
</script>

<style scoped>
.action-cards {
  display: flex;
  align-items: center;
}
.cardslot {
  display: flex;
}
.action-card {
  display: flex;
  justify-content: center;
  width: 2rem;
  height: 3rem;
  border: 2px var(--color-border) solid;
  cursor: pointer;
  margin: 0 0.02rem 0 0.02rem;
}
.act-a {
  align-self: center;
}
.act-u {
  align-self: flex-start;
}
.act-d {
  align-self: flex-end;
}
.act-x {
  align-self: center;
}
</style>
