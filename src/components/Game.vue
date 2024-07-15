<template>
  <div id="game">
    <div class="instructions" v-if="!readInstructions">
      <div>
        {{instructions}}
      </div>
      <div @click="hide" class="close">x</div>
    </div>
    <div class="grid-area" v-if="!timesUp">
      <div class="countdown">{{ minutes }}:{{ seconds }}</div>
      <div class="blown-counter">פוצצתי: {{ blownAsteroids }}</div>
      <div class="row" v-for="row in 5" :key="row">
        <div
          class="col"
          :id="`cell_${row}_${col}`"
          v-for="col in 5"
          :key="col"
          @click="blow(`cell_${row}_${col}`)"
        ></div>
      </div>
      <audio ref="audio">
        <source src="../assets/audio/explode.wav" type="audio/wav">
      </audio>
    </div>
    <div v-else>
        <div class="end">
        פוצצתם:  {{ blownAsteroids }}
        </div>
        <div class="again" @click="playAgain">למשחק חוזר לחצו עליי</div>
    </div>
  </div>
</template>

<script>
export default {
  name: "game",
  data() {
    return {
      words: ['baby', 'emo', 'old'],
      astroid: '',
      readInstructions: false,
      instructions: 'במשחק הבא תתבקשו לפוצץ אסטרואידים, פשוט תלחצו עליהם והם יתפוצצו, שימו לב שאתם לא חורגים מהזמנים!!!\nאם יהיו יותר מ-7 אסטרואידים על המסך, העולם יתפוצץ ותיפסלו\n',
      timer: 30,
      intervalId: null,
      intervalName: null,
      intervalCell: null,
      timesUp: false,
      blownAsteroids: 0, // Counter for blown asteroids
      asteroidsOnScreen: 0 // Counter for asteroids on screen
    };
  },
  computed: {
    minutes() {
      return Math.floor(this.timer / 60);
    },
    seconds() {
      return this.timer % 60 < 10 ? '0' + (this.timer % 60) : this.timer % 60;
    }
  },
  methods: {
    blow(id) {
      let cell = document.getElementById(id);
      if (cell.classList.contains("randomCell")) {
        cell.classList.add("noBackground");
        cell.classList.add("bomb");
        if (cell.classList.contains("randomCell")) {
          cell.classList.remove("randomCell");
        }
        setTimeout(() => {
          cell.classList.remove("bomb");
        }, 1000);

        // Play explosion sound
        const audio = this.$refs.audio;
        audio.currentTime = 0; // Reset the audio playback time to 0
        audio.play();

        // Increment the blown asteroids counter and decrement the asteroids on screen counter
        this.blownAsteroids++;
        this.asteroidsOnScreen--;
      }
    },
    chooseRandomCell() {
      if (this.asteroidsOnScreen < 8) { // Check if the number of asteroids on screen is less than 8
        const row = Math.floor(Math.random() * 5) + 1;
        const col = Math.floor(Math.random() * 5) + 1;
        const cellId = `cell_${row}_${col}`;
        const cell = document.getElementById(cellId);

        if (!cell.classList.contains("randomCell")) {
          cell.style.backgroundImage = `url(${document.location.href}/pouScreen/${this.astroid}/${this.astroid}.svg)`;
          cell.style.backgroundSize = "100% 100%";
          cell.style.backgroundRepeat = "no-repeat";
          cell.classList.add("randomCell");
          cell.classList.remove("noBackground");
          this.asteroidsOnScreen++;
        }
      } else {
        this.endGame();
      }
    },
    chooseRandomWord() {
      const randomIndex = Math.floor(Math.random() * this.words.length);
      this.astroid = this.words[randomIndex];
    },
    hide() {
      this.readInstructions = true;
      this.intervalCell = setInterval(this.chooseRandomCell, 650);
      this.intervalName = setInterval(this.chooseRandomWord, 650);
      this.startTimer();
    },
    startTimer() {
      this.intervalId = setInterval(() => {
        if (this.timer > 0) {
          this.timer--;
        } else {
          this.endGame();
        }
      }, 1000);
    },
    endGame() {
      clearInterval(this.intervalId);
      clearInterval(this.intervalCell);
      clearInterval(this.intervalName);
      this.timesUp = true;
    },
    playAgain() {
      this.timesUp = false;
      this.blownAsteroids= 0;// Counter for blown asteroids
      this.asteroidsOnScreen= 0;
      this.timer = 30;
      this.startTimer();
      this.intervalCell = setInterval(this.chooseRandomCell, 650);
      this.intervalName = setInterval(this.chooseRandomWord, 650);
      
    }
  },
};
</script>


<style scoped>
#game {
  width: 100vw;
  height: 100vh;
  background-image: url(../assets/media/startScreen/space-bg.png);
  background-size: 100vw 100vh;
  background-repeat: no-repeat;
}
.grid-area {
  display: grid;
  grid-template-columns: repeat(5, 1fr);
  grid-template-rows: repeat(5, 1fr);
  gap: 1px;
  width: 100%;
  height: 100%;
}
.row {
  display: contents;
}
.col {
  display: flex;
  justify-content: center;
  align-items: center;
}
@keyframes floatAnimation {
  0% {
    transform: translateY(0) scale(1);
  }
  50% {
    transform: translateY(-10px) scale(1.1);
  }
  100% {
    transform: translateY(0) scale(1);
  }
}
.noBackground {
  background-image: none !important;
  animation: none !important;
}
.bomb {
  background-image: url(@/assets/media/pouScreen/bomb.gif) !important;
  background-size: 100% 100%;
  background-repeat: no-repeat;
}
.randomCell {
  animation: floatAnimation 1.5s infinite ease-in-out;
}
.instructions {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  background-color: black;
  font-size: 4vmax;
  color: rgb(255, 208, 0);
  width: 80vw;
  padding: 7vw;
  border-radius: 25px;
  text-align: center;
  z-index: 3;
}
.close {
  position: absolute;
  top: 5%;
  right: 5%;
  cursor: pointer;
}
.countdown {
  position: absolute;
  top: 4px;
  left: 50%;
  transform: translateX(-50%);
  font-size: 2em;
  color: rgb(230, 15, 15);
  z-index: 2;
}

.blown-counter {
  position: absolute;
  top: 10px;
  left: 3vw;
  font-size: 1.5em;
  color: white;
  z-index: 2;
}

.end {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  font-size: 6vmax;
  color: rgb(255, 208, 0);
}

.again {
    position: absolute;
    bottom: 14vh;
    left: 30vw;
    background-color: rgb(0, 0, 0);
    width: 22vw;
    text-align: center;
    padding: 1rem 2rem;
    font-size: 3vmax;
     color: rgb(255, 208, 0);
    border: 2px solid #ffe81f;
    border-radius: 20px; /* Rounded borders */
    animation: floatAnimation 3s infinite ease-in-out;
}

@keyframes floatAnimation {
  0% {
    transform: translateY(0);
  }
  50% {
    transform: translateY(-10px);
  }
  100% {
    transform: translateY(0);
  }
}
</style>
