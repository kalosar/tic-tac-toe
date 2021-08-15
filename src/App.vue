<template>
  <div id="background" style="z-index: -10" class="absolute top-1/2 left-1/2 transform -translate-x-1/2 -translate-y-1/2 text-9xl font-sans font-bold text-center scale-[200%] opacity-100 select-none">
    <p class="text-blue-500 transform -rotate-6 scale-[150%] opacity-50">TIC</p>
    <p class="text-pink-500 transform rotate-6 scale-[150%] opacity-50">TAC</p>
    <p class="text-yellow-500 transform -rotate-6 scale-[150%] opacity-50">TOE</p>
  </div>

  <div class="flex select-none text-gray-800 h-screen">
    <div class="m-auto bg-white rounded-xl w-[500px] p-10 h-auto opacity-95">
      <section id="header" class="ml-7 mb-5 text-5xl">
        <p class="font-bold">Current: <span :class="'font-brush ' + symbolColor(playerTurn)">{{playerTurn}}</span></p>
      </section>
      <section id="board" class="m-auto grid grid-cols-3 gap-5 w-[25rem] h-[25rem] p-5">
        <div
          v-for="(tile, index) in board"
          :id="index"
          :key="index"
          @click="tileClicked(index, playerTurn)"
          :class="`text-center font-brush bg-gray-100 ${tile === 'B' ? 'hover:shadow-md' : 'shadow-inner'} rounded-lg overflow-hidden text-7xl leading-snug`"
        >
          <span :class="symbolColor(tile)">
            {{ tile }}
          </span>
        </div>
      </section>
      <section id="score" class="font-brush text-7xl flex justify-between mx-8 mt-4">
        <div :class="symbolColor('X')">X: <span class="font-sans font-bold text-gray-800">{{score['X']}}</span></div>
        <div :class="symbolColor('O')">O: <span class="font-sans font-bold text-gray-800">{{score['O']}}</span></div>
      </section>
    </div>
  </div>
</template>

<script setup>
import {ref} from 'vue'

const board = ref(['B', 'B', 'B', 'B', 'B', 'B', 'B', 'B', 'B'])

const paths = [
  // Rows
  [0, 1, 2],
  [3, 4, 5],
  [6, 7, 8],
  // Columns
  [0, 3, 6],
  [1, 4, 7],
  [2, 5, 8],
  // Diagonals
  [0, 4, 8],
  [2, 4, 6],
]

const score = ref({
  'X': 0,
  'O': 0
})

const movesLeft = ref(9)
const previousBeginner = ref('X')
const playerTurn = ref('X')

function symbolColor(symbol){
  if (symbol === 'X') return 'text-blue-500'
  else if (symbol === 'O') return 'text-pink-500'
  else return 'text-gray-100'
}

function tileClicked(index, player) {
  if (movesLeft.value === 0) return
  if (board.value[index] !== 'B') return

  movesLeft.value = movesLeft.value - 1
  board.value[index] = player
  playerTurn.value = player === 'X' ? 'O' : 'X'

  if (movesLeft.value >= 5) return

  const winner = checkStatus(index, player)

  if (!winner && movesLeft.value !== 0) return

  if (winner) {
    score.value[player]++
    playerTurn.value = player
    previousBeginner.value = player
  } else {
    playerTurn.value = previousBeginner.value === 'X' ? 'O' : 'X'
    previousBeginner.value = playerTurn.value
  }

  reset()
}

function reset() {
  board.value = Array(9).fill('B')
  movesLeft.value = 9
}

function checkStatus(index, player){
  return paths
    .filter(path => path.includes(index))
    .map(path => checkPath(path, player))
    .some(path => path === true)
}

function checkPath(path, player){
  const [x, y, z] = path
  return board.value[x] === player
      && board.value[y] === player
      && board.value[z] === player
}

</script>
