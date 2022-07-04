<template>
  <div id="board">
    <div id="display" :class="displayLength > 15 ? 'small' : 'regular'">{{ display }}</div>
    <Button v-for="button in buttons" :key="button" :name="button" @btn-click="btnClick"/>
  </div>
  {{displayLength}}
</template>

<script setup>
  import buttonsData from './data/Buttons'
  import Button from './components/Button.vue'
  import { ref, watch } from 'vue'

  const display = ref(0)
  let operatorPressed = ref(false)
  let calculation = ref('')
  let displayLength = ref(0)
  const buttons = buttonsData

  watch(display, () => {
    displayLength.value = display.value.length
  })

  const concatNum = (num) => {
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

  const noDuplicateOperator = () => {
    calculation.value = calculation.value.slice(0, -1)
  }

  const resetValues = () => {
    display.value = 0
    operatorPressed.value = false
    calculation.value = ''
  }

  const operator = (val) => {
    operatorPressed.value ? noDuplicateOperator() : ''
    switch(val) {
      case 'C':
        resetValues()
        break
      case '+/-':
        if(display.value === 0) return
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
        console.log(display.value.toString().length)
        if(display.value.length > 1) {
          display.value = display.value.toString().slice(0, -1)
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
  const btnClick = (value) => {
    (/[0-9 .]/).test(value) ? concatNum(value) : operator(value)
    
    console.log('Display: ', display.value)
    console.log('Calc: ', calculation.value)
    console.log('')
  }
</script>

<style>
  #display {
    width: 310px;
    height: 45px;
    padding: 20px 15px;
    text-align: right;
    font-size: 40px;
    background-color: black;
    color: white;
  }
  #display.regular {
    font-size: 40px;
  }
  #display.small {
    font-size: 20px;
    padding: 30px 15px 10px;
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
    /* background-image: radial-gradient(#555, #666); */
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
    /* background-image: radial-gradient(rgb(200, 155, 0), rgb(255, 165, 0)); */
  }
</style>