<template>
  <main>
    <div class="calculator w-full">
      <div class="w-full h-full flex flex-col justify-end items-center">
        <!-- History -->
        <div v-if="isExpanded" class="history w-full px-6 pt-3 flex-grow overflow-y-scroll">
          <div class="w-full block text-right py-1" v-for="item in _.initial(history)" :key="item">
            <p class="opacity-40">{{ item.operation }}</p>
            <p class="opacity-40">= {{ thousandSeparator(item.result) }}</p>
          </div>
        </div>

        <!-- Total -->
        <div class="total w-full px-6 pt-3 text-right">
          <div
              class="operation"
              :class="{ big: isBig('operation'), 'opacity-0': currentOperation === '' }"
          >
            {{ currentOperation ? currentOperation : 0 }}
          </div>
          <div class="result" :class="{ big: isBig('result') }">
            {{ result ? '= ' + thousandSeparator(result) : 0 }}
          </div>
        </div>

        <Separator/>

        <!-- Buttons -->
        <div class="buttons shrink-0 w-full p-3">
          <div class="grid grid-cols-4 gap-3">
            <Button class="colorized-button" @click="clear()">{{ clearButtonText }}</Button>
            <BackSpace @click="backspace()"/>
            <Button class="colorized-button" @click="operate('%')">%</Button>
            <Button class="colorized-button" @click="operate('/')">/</Button>
            <Button v-for="item in [7, 8, 9]" :key="item" @click="operand(item)">{{ item }}</Button>
            <XMark @click="operate('*')"/>
            <Button v-for="item in [4, 5, 6]" :key="item" @click="operand(item)">{{ item }}</Button>
            <Minus @click="operate('-')"/>
            <Button v-for="item in [1, 2, 3]" :key="item" @click="operand(item)">{{ item }}</Button>
            <Plus @click="operate('+')"/>
            <Button v-for="item in ['00', '0', '.']" :key="item" @click="operand(item)"
            >{{ item }}
            </Button>
            <Equal @click="equal()"/>
          </div>
        </div>
      </div>
    </div>
  </main>
</template>

<script setup>
import {ref, reactive} from 'vue'
import BackSpace from '@/components/BackSpace.vue'
import XMark from '@/components/XMark.vue'
import Minus from '@/components/Minus.vue'
import Plus from '@/components/Plus.vue'
import Equal from '@/components/Equal.vue'
import Button from '@/components/Button.vue'
import Separator from '@/components/Separator.vue'
import {evaluate} from 'mathjs'
import {_} from 'lodash'

const tgWebApp = window?.Telegram?.WebApp ?? {}
const isExpanded = ref(tgWebApp?.isExpanded ?? false)

const STATE_IN_PROGRESS = 'in_progress'
const STATE_DONE = 'done'
const state = ref(STATE_DONE)

const result = ref('')
const history = reactive([])

const clearButtonText = ref('C')
const currentOperation = ref('')

tgWebApp.onEvent('viewportChanged', function () {
  isExpanded.value = this.isExpanded
})

function operand(x) {
  changeClearButtonText('C')

  if (state.value === STATE_DONE) {
    reset()
  }

  /* Append operand */
  currentOperation.value = currentOperation.value + x + ''

  if (currentOperation.value !== '') {
    changeState(STATE_IN_PROGRESS)
    calculate()
  }
}

function operate(x) {
  if (state.value === STATE_DONE) {
    changeState(STATE_IN_PROGRESS)

    const temp = currentOperation.value + x + ''

    if (result.value !== '') {
      currentOperation.value = result.value + x + ''
    } else if (
        _.startsWith(temp, '/') ||
        _.startsWith(temp, '*') ||
        _.startsWith(temp, '+') ||
        _.startsWith(temp, '-') ||
        _.startsWith(temp, '%')
    ) {
      currentOperation.value = '0' + x + ''
    }
  } else {
    let value = currentOperation.value
    value = _.trimEnd(value, '/*+-')
    currentOperation.value = value + x + ''
    changeClearButtonText('C')
  }

  if (x === '%') {
    calculate()
  }
}

function equal() {
  calculate()
  changeState(STATE_DONE)
  addToHistory()
}

function calculate() {
  let value = currentOperation.value
  value = _.trimEnd(value, '/*-+')

  result.value = evaluate(value)
}

function clear() {
  if (clearButtonText.value === 'C') {
    reset()
    changeClearButtonText('AC')
  } else {
    history.values = history.splice(0)
  }
}

function changeClearButtonText(newText) {
  clearButtonText.value = newText
}

function backspace() {
  if (state.value === STATE_IN_PROGRESS) {
    currentOperation.value = currentOperation.value.slice(0, -1)

    if (currentOperation.value === '') {
      reset()
    } else {
      calculate()
    }
  }
}

function thousandSeparator(value) {
  value = value.toString().trim()
  const parts = _.split(value, '.')

  if (parts[1] !== undefined) {
    return parts[0].replace(/\B(?=(\d{3})+(?!\d))/g, ',') + '.' + parts[1]
  }

  return parts[0].replace(/\B(?=(\d{3})+(?!\d))/g, ',')
}

function isBig(item) {
  switch (item) {
    case 'operation':
      return state.value === STATE_IN_PROGRESS
    case 'result':
      return state.value === STATE_DONE
  }
}

function changeState(newState) {
  state.value = newState
}

function addToHistory() {
  history.push({
    operation: currentOperation.value,
    result: result.value
  })
}

function reset() {
  result.value = ''
  currentOperation.value = ''
  changeState(STATE_DONE)
}
</script>
