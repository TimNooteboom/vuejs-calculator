<template>
    <div :class="type" @click="buttonClick(name)">
        {{ name }}
    </div>
</template>

<script>
export default {
  name: '',
  emits: ['btnClick'],
  data() {
    return {
    }
  },
  props: {
    name: {
        type: String,
        default: '',
    },
    type: {
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

<style>

</style>