<template>
  <div>
    <div class="field">
      <div
        class="field__item"
        v-for="(key, i) in cells"
        :key="i"
        @click="userClick(i)"
      >
        <div :class="['field__item-mark', { 'cross' : cells[i] === 'X' }]">
          {{ key }}
        </div>
      </div>
    </div>
    <div class="info">
      <div v-show="gameOver" class="info__res">
        <div>Game over!</div>
        <div v-show="result === 'win'" class="info__res-win">You are win!</div>
        <div v-show="result === 'lose'" class="info__res-lose">You are lose...</div>
        <button class="info__res-restart" @click="restart">Новая игра</button>
      </div>
      Ход: {{ step }}.
      Ваш счёт: {{ accountUser }}.
      Счёт противника: {{ accountAI }}.
    </div>
  </div>
</template>

<script>
export default {
  name: 'HelloWorld',
  data () {
    return {
      cells: new Array(9),
      freeCells: [0, 1, 2, 3, 4, 5, 6, 7, 8],
      step: 0,
      userFirst: true,
      gameOver: false,
      accountUser: 0,
      accountAI: 0,
      result: null
    }
  },

  methods: {
    drawXO (key) {  
      if (this.cells[key] || this.gameOver) return;

      this.$set(this.cells, key, (this.step % 2) ? '0' : 'X');

      let index = this.freeCells.findIndex(v => v === key);
      this.freeCells.splice(index, 1);

      this.step++;
      if (this.step > 8) {
        this.gameOver = true;
        return;
      }
      if (this.checkWin()) {
        this.gameOver = true;
        
        if (this.stepAI) {
          this.accountUser++;
          this.result = 'win';
        } else {
          this.accountAI++;
          this.result = 'lose';
        }
      }
    },

    checkWin () {  
      const c = this.cells;
      if (c[0] && c[0] === c[1] && c[1] === c[2]) return true;
      if (c[3] && c[3] === c[4] && c[4] === c[5]) return true;
      if (c[6] && c[6] === c[7] && c[7] === c[8]) return true;

      if (c[0] && c[0] === c[3] && c[3] === c[6]) return true;
      if (c[1] && c[1] === c[4] && c[4] === c[7]) return true;
      if (c[2] && c[2] === c[5] && c[5] === c[8]) return true;

      if (c[0] && c[0] === c[4] && c[4] === c[8]) return true;
      if (c[2] && c[2] === c[4] && c[4] === c[6]) return true;
    },

    userClick (key) {
      if (this.gameOver || this.stepAI || this.cells[key]) return;
      this.drawXO(key);
      this.computerStep();
    },

    computerStep () {
      const rand = Math.floor(Math.random() * this.freeCells.length);

      setTimeout(() => {
        this.drawXO(this.freeCells[rand]);
      }, 500);
    },

    restart () {
      this.cells = new Array(9);
      this.freeCells = [0, 1, 2, 3, 4, 5, 6, 7, 8];
      this.step = 0;
      this.gameOver = false;
      this.result = null;
    }
  },

  computed: {
    stepAI: function() {
      return (this.userFirst && (this.step % 2)) || (!this.userFirst && (this.step % 2 === 0));
    }
  }
}
</script>


<style scoped>
  .field {
    display: inline-flex;
    flex-wrap: wrap;
    width: 300px;
    height: 300px;
  }

  .field__item {
    display: inline-flex;
    justify-content: center;
    align-items: center;
    box-sizing: border-box;
    width: calc(100px - 4px);
    height: calc(100px - 4px);
    margin: 2px;
    background-color: bisque;
    cursor: pointer;
  }

  .field__item-mark {
    font-size: 25px;
    font-weight: 700;
    color: brown;
  }

  .field__item-mark.cross {
    color: dodgerblue;
  }

  .info {
    margin: 10px;
  }

  .info__res-win {
    color: green;
  }

  .info__res-lose {
    color: brown;
  }
  .info__res-restart {
    margin: 10px;
    padding: 10px 20px;
    cursor: pointer;
  }
</style>
