<template>
   <div class="game-board" tabindex="0" v-on:keydown="onKeyDown">
      <div class="board-row" v-for="(row, rowIdx) in Board" :key="rowIdx">
         <div class="board-cell" :class="{
            'ball': cell === 1,
            'karett': cell === 2,
            'target': cell === 3
         }" v-for="(cell, cellIdx) in row" :key="cellIdx">
            &nbsp;
         </div>
      </div>
   </div>
</template>

<script setup>
import { onBeforeMount, onMounted, reactive, ref, defineEmits } from 'vue';

const emit = defineEmits(['score']);

const Board = reactive([]);
const maxX = 40;
const maxY = 37;
let Karret = reactive([]);
let interval = 0;
let ballPosition = Math.floor(maxX / 2)

onBeforeMount(() => {
   InitBoard();
   InitBall()
   InitTarget()
   InitKarett(0)
   RenderKarret()
   window.addEventListener('keydown', onKeyDown);
});

onMounted(() => {
   interval = setInterval(() => RenderKarret(), 10);
});

function InitBoard() {
   for (let y = 0; y < maxY; y++) {
      let row = [];
      for (let x = 0; x < maxX; x++) {
         row.push(0);
      }
      Board.push(row);
   }
}
function InitTarget() {
   for (let i = 2; i < maxX - 2; i++) {
      for (let g = 2; g < maxY / 3; g++) {
         Board[g][i] = 3
      }
   }
}

function InitKarett(startPosition) {
   for (let i = startPosition; i < startPosition + 5; i++) {

      Karret.push({
         col: Math.floor(maxX / 2 + 2) - i,
         row: maxY - 2,
         seg: 2
      });
   }
}

function RenderKarret(startPosition) {
   InitKarett(startPosition)
   Karret.forEach((cell) => Board[cell.row][cell.col] = cell.seg);
}

function MoveKarretInRight() {
   let lastSegment = Karret.pop();
   lastSegment.col += 1;
   Karret.unshift(lastSegment);
}

function MoveKarretInLeft() {
   let firstSegment = Karret.shift();
   firstSegment.col -= 1;
   Karret.push(firstSegment);
}

function onKeyDown(evt) {
   switch (evt.key) {
      case 'ArrowLeft':
         MoveKarretInLeft();
         break;
      case 'ArrowRight':
         MoveKarretInRight();
         break;
   }
}


function GameOver() {
   console.log('qwerty')
}



function InitBall() {
   for (let i = ballPosition; i < ballPosition + 1; i++) {
      Board[maxY - 3][i] = 1;
   }
}
</script>

<style>
.board-cell {
   width: 20px;
   height: 20px;
   display: inline-block;
   border: 1px dotted #ccc;
}

.ball {
   background-color: red;
}

.karett {
   background-color: blue;
}

.target {
   background-color: green;
}
</style>