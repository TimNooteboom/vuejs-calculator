<template>
  <div class="button" :class="{'operator': operators.includes(name)}" @click="buttonClick(name)">
    {{ props.name }}
  </div>
</template>

<script setup>
  import { reactive } from 'vue'

  const emit = defineEmits(['btnClick'])
  const operators = reactive(['C', '+/-', '%', '/', 'x', '-', '+', '=', 'del'])

  const props = defineProps ({
    name: String
  })

  const buttonClick = (btn) => emit('btnClick', btn)

</script>