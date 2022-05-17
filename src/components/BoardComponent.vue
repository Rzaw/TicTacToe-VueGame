<template>
  <div class="container">
    <div class="row">
      <div class="col">
        <h1>Board game</h1>
        <h2 v-if="winner">Winner - {{ winner }} 🥳</h2>
        <h2 v-else>Player {{ player }} move</h2>
        <button @click="reset" class="btn btn-secondary mb-3">Reset</button>

        <div v-for="(x, xIndex) in board" :key="x" class="row">
          <button
            v-for="(y, yIndex) in x"
            :key="y"
            class="col-lg-1 square"
            @click="move(xIndex, yIndex)"
          >
            {{ y }}
          </button>
        </div>
      </div>
      <div class="col-3">
        <div class="d-flex justify-content-between mt-3">
          <h2>History</h2>
          <button @click="resetHistory" class="btn btn-secondary">
            Reset History
          </button>
        </div>
        <div v-for="(game, index) in history" :key="index">
          Game #{{ index + 1 }} won by {{ game }}
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { computed, onMounted, ref, watch } from "vue";

const calculateWinner = (board) => {
  const lines = [
    [0, 1, 2],
    [3, 4, 5],
    [6, 7, 8],
    [0, 3, 6],
    [1, 4, 7],
    [2, 5, 8],
    [0, 4, 8],
    [2, 4, 6],
  ];

  for (let i = 0; i < lines.length; i++) {
    const [a, b, c] = lines[i];
    if (board[a] && board[a] === board[b] && board[a] === board[c])
      return board[a];
  }
  return null;
};

export default {
  setup() {
    const player = ref("X");
    const board = ref([
      ["", "", ""],
      ["", "", ""],
      ["", "", ""],
    ]);

    const winner = computed(() => calculateWinner(board.value.flat()));

    const move = (x, y) => {
      if (winner.value) return;
      board.value[x][y] = player.value;
      player.value = player.value === "O" ? "X" : "O";
    };

    const reset = () => {
      player.value = "X";
      board.value = [
        ["", "", ""],
        ["", "", ""],
        ["", "", ""],
      ];
    };

    const history = ref([]);

    watch(winner, (curr, prev) => {
      if (curr && !prev) {
        console.log(curr);
        history.value.push(curr);
        localStorage.setItem("history", JSON.stringify(history.value));
      }
    });

    onMounted(() => {
      history.value = JSON.parse(localStorage.getItem("history")) ?? [];
    });

    const resetHistory = () => {
      history.value = [];
      localStorage.setItem("history", JSON.stringify(history.value));
    };

    return { winner, player, board, move, reset, history, resetHistory };
  },
};
</script>

<style>
.square {
  background: #fff;
  border: 1px solid black;
  float: left;
  font-size: 70px;
  font-weight: bold;
  line-height: 34px;
  height: 100px;
  width: 100px;
}
</style>