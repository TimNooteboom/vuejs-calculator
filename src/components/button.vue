<template>
  <div class="button" :class="{'operator': operators.includes(name)}" @click="buttonClick(name)">
    {{ name }}
  </div>
</template>

<script>
export default {
  name: 'Button',
  emits: ['btnClick'],
  data() {
    return {
      operators: ['C', '+/-', '%', '/', 'x', '-', '+', '=', 'del']
    }
  },
  props: {
    name: {
      type: String,
      default: '',
    },
  },
  methods: {
    buttonClick(btn) {
      this.$emit('btnClick', btn)
    }
  },
}
</script>