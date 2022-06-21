<template>
  <div id="board">
    <div id="display">{{ display }}</div>
    <Button v-for="button in buttons" :key="button" :name="button" @btn-click="btnClick"/>
  </div>
</template>

<script setup>
  import buttonsData from './data/Buttons'
  import Button from './components/button.vue'
  import {ref} from 'vue'

  const display = ref(0)
  let operatorPressed = ref(false)
  let calculation = ref('')
  const buttons = buttonsData

  function concatNum(num) {
    if (display.value === 0 && parseInt(num) === 0) {
      return
    }

    if(num === '.' && display.value.indexOf('.') > -1) return
    if(num === '.' && display.value === 0) {
      display.value = '0'
      calculation.value = '0.'
    } else {
      calculation.value += num
    }

    if(operatorPressed.value) {
      display.value = 0
    }
    operatorPressed.value = false
    display.value === 0 ? (display.value = num) : (display.value += num)
  }

  function noDuplicateOperator() {
    calculation.value = calculation.value.slice(0, -1)
  }

  function resetValues() {
    display.value = 0
    operatorPressed.value = false
    calculation.value = ''
  }

  function operator(val) {
    operatorPressed.value ? noDuplicateOperator() : ''
    switch(val) {
      case 'C':
        resetValues()
        break
      case '+/-':
        if(display.value.toString().charAt(0) === '-') {
          display.value = display.value.toString().slice(1)
        } else {
          display.value = `-${display.value}`
        }
        calculation.value = display.value
        break
      case '%':
        display.value = parseFloat(display.value) / 100
        calculation.value = display.value
        break
      case 'del':
        if(display.value.length > 1) {
          display.value = display.value.slice(0, -1)
          calculation.value = display.value
        } else {
          resetValues()
        }
        break
      case 'x':
        calculation.value += '*'
        operatorPressed.value = true
        break
      case '/':
      case '-':
      case '+':
        calculation.value += val
        operatorPressed.value = true
        break
      case '=':
        console.log('Equals: ', calculation.value)
        display.value = eval(calculation.value)
        operatorPressed.value = false
        calculation.value = display.value
        break
    } 
  }

  // this is the value we get back from the button component
  function btnClick(value) {
    (/[0-9 .]/).test(value) ? concatNum(value) : operator(value)
    
    console.log('Display: ', display.value)
    console.log('Calc: ', calculation.value)
    console.log('')
  }
</script>

<style>
#display {
  width: 100%;
  padding: 10px 15px;
  text-align: right;
  font-size: 35px;
  background-color: black;
  color: white;
}
#board {
  margin: 0 auto;
  font-size: 25px;
  display: flex;
  flex-wrap: wrap;
  align-items: center;
  justify-content: center;
  gap: 3px;
  width: 338px;
}
.button, .operator {
  border: 1px solid black;
  color: #fff;
  background-color: #666;
  background-image: radial-gradient(#555, #666);
  width: 80px;
  height: 50px;
  padding-top: 20px;
  text-align: center;
}
.button:hover, .operator:hover {
  opacity: 0.9;
  cursor: pointer;
}
.operator {
  background-color: rgb(255, 165, 0);
  background-image: radial-gradient(rgb(200, 155, 0), rgb(255, 165, 0));
}
</style>