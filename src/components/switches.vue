<template>
  <ul class="switches">
    <li
      class="switch-item"
      v-for="(item,index) in switches"
      :key="index"
      :class="{'active': currentIndex === index}"
      @click="switchItem(index)"
    >
      <span>{{item.name}}</span>
    </li>
  </ul>
</template>

<script type="text/ecmascript-6">
export default {
  props: {
    switches: {
      type: Array,
      default: []
    },
    currentIndex: {
      type: Number,
      default: 0
    }
  },
  methods: {
    switchItem(index) {
      this.$emit('switch', index)
    }
  }
}
</script>

<style scoped lang="scss">
.switches {
  display: flex;
  align-items: center;
  width: 240px;
  margin: 0 auto;
  border: 1px solid #fff;
  border-radius: 5px;
  .switch-item {
    flex: 1;
    padding: 8px;
    text-align: center;
    font-size: 14px;
    color: rgba(255, 255, 255, 0.616);
    &.active {
      background: rgba(255,255,255,0.8);
      color: rgba(221, 27, 27, 1);;
    }
  }
}
</style>