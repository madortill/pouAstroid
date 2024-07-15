<template>
  <div id="star-wars">
    <div v-if="firstTime">
        <div v-show="!didInteract">
          <div class="start" @click="addLogo"></div>
          <div class="textToStart">לחצ/י עליי להתחלה</div>
        </div>
        <audio ref="audio" loop>
        <source src="../assets/audio/starwars.mp3" type="audio/mp3">
        </audio>
        <div class="text" v-show="beforeText && didInteract">לפני שנים רבות...</div>
        <img src="../assets/media/startScreen/starwars-logo.png" id="logo" v-show="!beforeText" />
        <div class="titles" v-show="afterImage">
        <p v-for="(title, index) in displayedTitles" :key="index" class="title" :style="{ animationDelay: getDelay(index) + 's' }">{{ title }}</p>
        </div>
        <div v-if="titlesFinished" class="end-message">
            <button class="star-wars-button" id="again" @click="nextPage">רגע, מה המשימה?</button>
            <button class="star-wars-button" id="next" @click="nextPage">
              <p>למשחק</p>
            </button>
        </div>
    </div>

  </div>
</template>

<script>
export default {
  name: "star-wars",
  data() {
    return {
      didInteract: false,
      isPlaying: false,
      beforeText: true,
      afterImage: false,
      titlesFinished: false,
      firstTime: true,
      titlesArray: [
        'אי שם מזמן, נוצר לו יצור מרושע... האסטרואיד.',
        'מטרתו היא להשמיד את כדור הארץ ואנחנו בברוגז איתו ממש. ואם זה לא מספיק, אז הוא גם נראה רע הוא מלא בבליטות',
        ' הלומדה שתעברו כעת מאוד חשובה בגלל הסכנה היומיומית שנשקפת לכדור הארץ. עשרות אלפי אסטרואידים נעים ברגע זה לכיוון הכוכב שלנו, ומטרתם - להשמיד אותנו!!!!! ',
        'כדי להגן על הכוכב, עליכם ללמוד את האסטרואיד, וממש לגדל אותו כאילו הוא בנכם הביולוגי. ',
        'אבל אל תתבלבלו, הם מרושעים. האם אתם מוכנים????????????????'
      ],
      displayedTitles: [],
      currentTitleIndex: 0
    }
  },
  methods: {
    addLogo() {
      this.didInteract = true;
      setTimeout(() => {
        this.beforeText = false;
        this.playAudio();
      }, 5000);
    },
    playAudio() {
      const audio = this.$refs.audio;
      audio.currentTime = 8;
      audio.play()
        .then(() => {
          this.isPlaying = true;
        })
        .catch(error => {
          console.error('Failed to play audio:', error);
        });
      document.getElementById("logo").classList.add("animate");
      setTimeout(() => {
        this.afterImage = true;
        this.startAddingTitles();
      }, 10000);
    },
    startAddingTitles() {
      this.currentTitleIndex = 0;
      this.displayedTitles = [];
      this.titlesFinished = false;

      const addTitle = () => {
        if (this.currentTitleIndex < this.titlesArray.length) {
          this.displayedTitles.push(this.titlesArray[this.currentTitleIndex]);
          this.currentTitleIndex++;
           setTimeout(addTitle(),800);
        } else {
          setTimeout(() => {
            this.titlesFinished = true;
            }, 22000);
        }
      };

      addTitle();
    },
    getDelay(index) {
        return index * 0.5; // Adjust the delay factor for desired timing
    },
    startOver() {
        this.firstTime= false;
        setTimeout(()=>{
            this.firstTime= true;
        }, 40)
        },
   nextPage(event) {
      const audio = this.$refs.audio;
      if (audio) {
        audio.pause();
        audio.currentTime = 0; // Reset the audio to the start
      }
      if (event.currentTarget.id === "next") {
        this.$emit("switch-screen", 3);
      } else {
        this.startOver();
      }
    }
  }
};
</script>

<style scoped>
#star-wars {
  background-color: black;
  font-size: 3vmax;
  width: 100vw;
  height: 100vh;
  color: rgb(255, 208, 0);
  overflow: hidden;
}

.text {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  font-size: 4vmax;
  color: lightblue;
  opacity: 0;
  animation: fadeInOut 5s ease-in forwards;
}

#logo {
  position: absolute;
  width: 100vw;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  opacity: 0;
  cursor: pointer;
}

.start {
  position: relative;
  top: 40vh;
  right: 35vw;
  background-image: url(../assets/media/startScreen/rocket.png);
  background-size: 100% 100%;
  background-repeat: no-repeat;
  cursor: pointer;
  width: 35vw;
  height: 35vw;
  animation: floatAnimation 1.5s infinite ease-in-out;
}

.textToStart {
    position: relative;
    top: 40vh;
    right: 31vw;
    cursor: pointer;
    animation: floatAnimation 1.5s infinite ease-in-out;
}


.animate {
  animation: shrink 10s linear;
}

@keyframes fadeInOut {
  0% {
    opacity: 0;
  }
  50% {
    opacity: 1;
  }
  100% {
    opacity: 0;
  }
}

@keyframes shrink {
  0% {
    transform: translate(-50%, -50%) scale(1);
    opacity: 1;
  }
  100% {
    transform: translate(-50%, -50%) scale(0);
    opacity: 0;
  }
}

.title {
  font-size: 5vmax;
  opacity: 0;
  animation: fadeInShrink 40s linear forwards;
  margin-top: 1em;
}

@keyframes fadeInShrink {
  0% {
    opacity: 1;
    transform: translateY(100vh) scale(1);
  }
  30% {
    opacity: 1;
    transform: translateY(0) scale(1);
  }
  70% {
    opacity: 1;
    transform: translateY(-200vh) scale(0.5);
  }
  100% {
    opacity: 0;
    transform: translateY(-200vh) scale(0.5);
  }
}

.titles {
  overflow: hidden;
  height: 100vh;
  width: 100vw;
  text-align: center;
  transform-origin: 50% 100%;
  transform: perspective(200px) rotateX(15deg);
}

.end-message {
  display: flex;
  /* justify-content: center; */
  position: absolute;
  top: 10vh;
  left: 50%;
  transform: translateX(-50%);
}

.star-wars-button {
  font-family: "kshafim";
  margin: 1rem;
  color: rgb(255, 208, 0);
  text-align: center;
  padding: 1rem 2rem;
  font-size: 4vmax;
  background-color: #000;
  border: 2px solid #ffe81f;
  border-radius: 20px; /* Rounded borders */
  position: relative;
  overflow: hidden;
  animation: floatAnimation 3s infinite ease-in-out;
}

#next p {
   position: relative;
   left: 4vw;
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

.star-wars-button::before {
  content: '';
  position: absolute;
  top: 50%;
  left: 50%;
  width: 300%;
  height: 300%;
  background: rgba(255, 232, 31, 0.1);
  transition: all 0.5s ease;
  transform: translate(-50%, -50%) scale(0);
}

.star-wars-button:hover::before {
  transform: translate(-50%, -50%) scale(1);
}

.star-wars-button:hover {
  background-color: #333; /* Darker background on hover */
  border-color: #f0c60f; /* White border on hover */
}

.star-wars-button:active {
  transform: scale(0.95);
}

</style>
