<script setup>
import { ref, computed } from "vue";

const status = ref(1)
const current = ref(1);
const currentColor = computed(() => (current.value === 1 ? "#000" : "#fff"));
const chessboard = ref([
  [0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0],
  [0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0],
  [0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0],
  [0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0],
  [0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0],
  [0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0],
  [0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0],
  [0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0],
  [0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0],
  [0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0],
  [0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0],
  [0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0],
  [0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0],
  [0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0],
  [0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0],
]);

function showTag(i, j) {
  if (i === 7 && j === 7) {
    return true;
  }
  if (i === 3 && (j === 3 || j === 11)) {
    return true;
  }

  if (i === 11 && (j === 3 || j === 11)) {
    return true;
  }
}

function handleDrop(i, j) {
  if(status.value === 0) return 
  if (chessboard.value[i][j] !== 0) return;

  chessboard.value[i][j] = current.value;

  check(i, j);

  if (current.value === 1) {
    current.value = 2;
  } else {
    current.value = 1;
  }
}

function check(i, j) {
  const player = current.value;

  const directions = [
    [0, 1], // 水平方向
    [1, 0], // 垂直方向
    [1, 1], // 左上到右下方向
    [1, -1], // 右上到左下方向
  ];

  for (const [dx, dy] of directions) {
    let count = 1;
    let row = i + dx;
    let col = j + dy;

    while (
      row >= 0 &&
      row < 15 &&
      col >= 0 &&
      col < 15 &&
      chessboard.value[row][col] === player
    ) {
      count++;
      row += dx;
      col += dy;
    }

    row = i - dx;
    col = j - dy;
    while (
      row >= 0 &&
      row < 15 &&
      col >= 0 &&
      col < 15 &&
      chessboard.value[row][col] === player
    ) {
      count++;
      row -= dx;
      col -= dy;
    }

    if (count >= 5) {
      status.value = 0
      alert((current.value === 1 ? 'black' : 'white') + ' win')
      return;
    }
  }
}
</script>

<template>
  <div class="board">
    <div class="row" v-for="(row, i) in chessboard" :key="i">
      <div class="col" v-for="(col, j) in row" :key="j">
        <div
          class="content"
          :class="{ w: col === 2, b: col === 1 }"
          @click="handleDrop(i, j)"
        ></div>
        <div class="tag" v-if="showTag(i, j)"></div>
      </div>
    </div>
  </div>
</template>

<style lang="less" scoped>
.board {
  .row {
    display: flex;
    flex-wrap: wrap;
    width: 450px;

    &:first-child {
      .col {
        // 纵线
        &::before {
          top: calc(50% - 1px);
        }
      }
    }

    &:last-child {
      .col {
        // 纵线
        &::before {
          top: calc(-50% + 1px);
        }
      }
    }

    .col {
      width: 30px;
      height: 30px;
      background: #efaf68;
      position: relative;

      &:first-child {
        // 横线
        &::after {
          left: calc(50% - 1px);
        }
      }

      &:last-child {
        // 横线
        &::after {
          left: calc(-50% + 1px);
        }
      }

      // 横线
      &::after {
        content: "";
        background: #000;
        width: 100%;
        height: 2px;
        position: absolute;
        left: 0;
        top: calc(50% - 1px);
      }

      // 纵线
      &::before {
        content: "";
        background: #000;
        width: 2px;
        height: 100%;
        position: absolute;
        left: calc(50% - 1px);
        top: 0;
      }

      .content {
        cursor: grab;

        opacity: 0.4;
        width: 90%;
        height: 90%;
        position: absolute;
        left: 50%;
        top: 50%;
        transform: translate(-50%, -50%);
        border-radius: 100%;
        z-index: 9;

        &:hover {
          background: v-bind(currentColor);
        }

        &.w {
          opacity: 1;
          background: #fff;
        }

        &.b {
          opacity: 1;
          background: #000;
        }
      }

      .tag {
        width: 25%;
        height: 25%;
        background: none;
        position: absolute;
        left: 50%;
        top: 50%;
        transform: translate(-50%, -50%);
        border-radius: 100%;
        z-index: 2;
        background: #000;
      }
    }
  }
}
</style>
