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

  const display = ref('0')
  let operatorPressed = ref(false)
  let lastPressedWasOperator = ref(false)
  let displayLength = ref(0)
  let leftHandValue = ref('')
  let calculation = null
  let rightHandValue = ref('')
  const buttons = buttonsData

  watch(display, () => {
    displayLength.value = display.value.length
  })

  const concatNum = (num) => {
    // don't show multiple zeros 
    if (display.value === '0' && num === '0') {
      return
    }

    // don't show multiple dots in a value
    if(num === '.' && ['.'].includes(display.value)) return

    // if first value is a dot, preceed with a zero
    if(num === '.' && display.value === '0') {
      display.value = '0.'
    } else {
      display.value === '0' ? (display.value = num) : (display.value += num)
    }

    if(lastPressedWasOperator.value) {
      display.value = num
      lastPressedWasOperator.value = false
    }

    if(operatorPressed.value) {
      rightHandValue.value = display.value
    } else {
      leftHandValue.value = display.value
    }
    console.log({left: leftHandValue.value, right: rightHandValue.value})
  }

  const resetValues = () => {
    display.value = '0'
    operatorPressed.value = false
    lastPressedWasOperator.value = false
    leftHandValue.value = '0'
    rightHandValue.value = '0'
  }

  const setOperator = () => {
    operatorPressed.value = true
    leftHandValue.value = display.value
  }

  const cancelOperation = () => {
    leftHandValue = display.value
    operatorPressed.value = false
    lastPressedWasOperator.value = false
  } 

  const divideByZero = () => {
    if(calculation?.toString().indexOf('/') > -1 && rightHandValue.value === '0') {
      resetValues()
      display.value = "You can't divide by zero, silly!"
      setTimeout(() => { resetValues() },3000)
      return true
    }
    return false
  }

  const operator = (val) => {
    lastPressedWasOperator.value = true

    switch(val) {
      case 'C':
        resetValues()
        break
      case '+/-':
        if(display.value === '0') return
        if(display.value.toString().charAt(0) === '-') {
          display.value = display.value.toString().slice(1)
        } else {
          display.value = `-${display.value}`
        }
        cancelOperation()
        break
      case '%':
        display.value = parseFloat(display.value) / 100
        cancelOperation()
        break
      case 'del':
        if(display.value.length > 1) {
          display.value = display.value.toString().slice(0, -1)
          cancelOperation()
        } else {
          resetValues()
        }
        break
      case 'x':
        calculation = (a,b) => a * b
        setOperator()
        break
      case '/':
        calculation = (a,b) => a / b
        setOperator()
        break
      case '-':
        calculation = (a,b) => a - b
        setOperator()
        break
      case '+':
        calculation = (a,b) => a + b
        setOperator()
        break
      case '=':
        if(operatorPressed.value && !divideByZero()) {
          display.value = calculation(parseFloat(leftHandValue.value), parseFloat(rightHandValue.value))
          operatorPressed.value = false
        }
        break
    } 
  }

  // this is the value we get back from the button component each time one is clicked
  const btnClick = (value) => {
    (/[0-9 .]/).test(value) ? concatNum(value) : operator(value)
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