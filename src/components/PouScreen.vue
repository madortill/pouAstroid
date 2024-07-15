<template>
  <div id="pou-screen" :style="{ 'background-image': 'url(' + backgroundImg + ')' }">
    <iframe class="animation2" v-show="showAnimation2" :src="playerAnimation"></iframe>
    <iframe class="animation" v-if="showAnimation1" :src="preloader"></iframe>
    <div v-else>
      <icons-for-pou :age="age" :level="level" v-show="!showAnimation2"></icons-for-pou>
       <audio ref="audio">
        <source src="../assets/audio/good-job.wav" type="audio/wav">
      </audio>
        <div class="instructions" v-if="!readInstructions">
        <div> 
            {{instructions[astroid]}}
        </div>
        <div @click="hide" class="close">x</div>
        </div>
        <div v-if="!showAnimation2">
        <img 
            :id="astroid" 
            :class="astroid" 
            :src="playerImg"
            @drop="drop1"
            @dragover="allowDrop"
            @touchend="dropTouch"
        />
        <img  
            id="item" 
            :class="[`${astroid}Item`] "
            :src="playerItem"
            alt="item"
            draggable="true" 
            @dragstart="drag1"
            @touchstart="touchStart"
            @touchmove="touchMove"
            @touchend="touchEnd"
        />
        <img v-if="astroid === 'emo'" id="bubbles" src="../assets/media/pouScreen/emo/bubble.gif"/>

        </div>
        <div class="typewriter" v-show="afterDone">
           <div class="lesson" >{{lessons[astroid]}}</div>
        </div>
       
    </div>
  </div>
</template>
<script>
import IconsForPou from '@/components/IconsForPou.vue';


export default {
  name: "pou-screen",
  props: ['astroid'],
  data() {
    return {
      showAnimation1: true,
      showAnimation2: false,
      readInstructions: false,
      touchItem: null,
      touchX: 0,
      touchY: 0,
      instructions: {
        "baby": "אוי לא! נפל לבייבי אסטרואיד המוצץ!!!!!\nבלעדיו הוא לא יכול לישון ולהירדם כדי לגדול...\nבבקשה תביאו לו את המוצץ ותעזרו לו לגדול",
        "emo": "המתבגר האסטרואיד לא התקלח כבר הרבה זמן​\nתעזרו לו להימנע מחצקונים ותנקו אותו שהחדר לא יהיה מחניק ",
        "old": "האסטרואיד הזקן כבר לא זוכר כלום!!!!!​ הוא אפילו שכח איפה הוא שם את שלט הטלוויזיה בסלון שלו והוא ממש רוצה להעביר ערוץ... סרקו את החדר כדי למצוא היכן מתחבא השלט ולאחר מכן הביאו לו אותו"
      },
      age: "בייבי",
      level: 1,
      afterDone: false,
      lessons: {
        "baby": "זה האסטרואיד התינוק. ​\nהוא אמנם רק נראה תמים אבל הוא הורס שכונות מקומיות בגלל שהוא ממש קטן",
        "emo": "האסטרואיד המתבגר הוא די חרא כי הוא בכוונה מנסה להפציץ מקומות שהוא לא אוהב",
        "old": "האסטרואיד הזקן אמנם לא זוכר כלום אבל הוא גדול מספיק כדי להשמיד את כל כדור הארץ"
      },
    }
  },
  components: {
    IconsForPou
  },
  methods: {
    allowDrop(ev) {
      if (this.readInstructions) {
        ev.preventDefault();
      }
    },
    drag1(ev) {
      if (this.readInstructions && ev.target.id === "item") {
        ev.dataTransfer.setData("img", ev.target.id);
      } 
    },
    drop1(ev) {
      if (this.readInstructions) {
        ev.preventDefault();
        let data1 = ev.dataTransfer.getData("img");
        let element = document.getElementById(data1);

        if (element && ev.target.id === this.astroid) {
          ev.target.appendChild(element);
          this.showAnimation2 = true; // Update state here
        }
      }
    },
    touchStart(ev) {
      if (this.readInstructions && ev.target.id === "item") {
        this.touchItem = ev.target;
        this.touchX = ev.touches[0].clientX - ev.target.offsetLeft;
        this.touchY = ev.touches[0].clientY - ev.target.offsetTop;
      }
      if (this.astroid === "old") {
        document.getElementById("item").style.cssText = " z-index: 3;"
      }

    },
touchMove(ev) {
    if (this.readInstructions && this.touchItem) {
        ev.preventDefault();
        const leftInPixels = ev.touches[0].clientX - this.touchX;
        const topInPixels = ev.touches[0].clientY - this.touchY;
        this.touchItem.style.position = 'absolute';
        this.touchItem.style.left = `${leftInPixels}px`;
        this.touchItem.style.top = `${topInPixels}px`;

        if (this.astroid === 'emo') {
            const emoAstroid = document.getElementById(this.astroid);
            const astroidRect = emoAstroid.getBoundingClientRect();
            if (
                leftInPixels >= astroidRect.left &&
                leftInPixels <= astroidRect.right &&
                topInPixels >= astroidRect.top &&
                topInPixels <= astroidRect.bottom
            ) {
                document.getElementById('bubbles').style.display = 'block';
            } else {
                document.getElementById('bubbles').style.display = 'none';
            }
        }
    }
}

,
    touchEnd(ev) {
      if (this.readInstructions && this.touchItem) {
        // Temporarily hide the touch item to correctly identify the drop target
        this.touchItem.style.display = 'none';
        // Get the element at the touch end position
        let dropTarget = document.elementFromPoint(ev.changedTouches[0].clientX, ev.changedTouches[0].clientY);
        // Restore the touch item visibility
        this.touchItem.style.display = 'block';
        if (dropTarget && dropTarget.id === this.astroid || dropTarget.id === "bubbles") {
          // console.log(dropTarget);
          dropTarget.appendChild(this.touchItem);
          this.touchItem.style.position = 'initial';
        if (this.astroid === "emo") {
            document.getElementById("bubbles").style.display = "none";
          }
          this.afterDone = true;
          setTimeout( () => {
            this.afterDone = false;
            this.showAnimation2 = true;
            this.playAudio();
          }, 7000);
          setTimeout(() => {
            this.showAnimation2 = false;
            this.readInstructions = false;
            this.level++;
            if (this.astroid === "baby") {
              this.age = "אימו";
              this.$emit("grow-up", "emo");
            } else if (this.astroid === "emo") {
              this.age = "זקן";
              this.$emit("grow-up", "old");
            } else {
              this.$emit("grow-up", "done");
            }
          }, 9000); // Update state here
        }
        this.touchItem = null;
      }
    },
    dropTouch(ev) {
      ev.preventDefault();
    },
    hide() {
      this.readInstructions = true;
    },
    playAudio() {
      const audio = this.$refs.audio;
      audio.currentTime = 0;
      audio.play()
        .then(() => {
          this.isPlaying = true;
        })
        .catch(error => {
          console.error('Failed to play audio:', error);
        });
    }
  },
  computed: {
    preloader() {
      return `${document.location.href}/animations/preloader/preloader.html`
    },
    playerAnimation() {
      return `${document.location.href}/animations/${this.astroid}/${this.astroid}.html`;
    },
    playerImg() {
      return `${document.location.href}/pouScreen/${this.astroid}/${this.astroid}.svg`;
    },
    backgroundImg() {
      return `${document.location.href}/pouScreen/${this.astroid}/${this.astroid}Room.png`;
    },
    playerItem() {
      return `${document.location.href}/pouScreen/${this.astroid}/item.png`;
    }
  },
  created() {
    setTimeout(() => {
      this.showAnimation1 = false;
    }, 3000);
  }
}
</script>


<style scoped>
#pou-screen {
  width: 100vw;
  height: 100vh;
  background-size: 100vw 100vh;
  background-repeat: no-repeat;
  overflow: hidden;
}

.animation {
  width: 100vw;
  height: 100vh;  
  display: block;
  background-color: black; 
}


.animation2 {
  position: absolute;
  top: 0;
  width: 100vw;
  height: 100vh;  
  display: block;
  background-image: url(../assets/media/pouScreen/winner-bg.png);
  background-size: 100vw 100vh;
  background-repeat: no-repeat;
  overflow: hidden;
}

.baby {
  position: absolute;   
  height: 28vh;
  left: -13vw;
  bottom: 24vh;
}

.emo {
  position: absolute;
  height: 28vh;
  left: 22vw;
  bottom: 20vh;
}

.old {
  position: absolute;
  height: 28vh;
  left: 24vw;
  bottom: 24vh;
  z-index: 2;
}

.babyItem {
  position: absolute;
  right: 28vw;
  bottom: 0;
  height: 16vh;
}

.emoItem {
  position: absolute;
  right: 34vw;
  bottom: 5vh;
  height: 16vh;
  rotate: 24deg;
  z-index: 4;
}

#bubbles {
  position: absolute;
  right: 19vw;
  bottom: 20vh;
  width: 60vw;
  height: 60vw;
  display: none;
}

.oldItem {
  position: absolute;
  left: 21vw;
  bottom: 15vh;
  height: 16vh;
  z-index: 1;
  rotate: -24deg;
}

 .instructions {
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
    background-color: black;
    font-size: 4vmax;
    color: rgb(255, 208, 0);
    width: 70vw;
    padding: 7vw;;
    border-radius: 25px;
    text-align: center;
    z-index: 3;
 }

 .lesson {
    position: absolute;
    bottom: 10vh;
    right: 8vw;
    background-color: black;
    font-size: 4vmax;
    color: rgb(255, 208, 0);
    width: 70vw;
    padding: 7vw;;
    border-radius: 25px;
    text-align: center;
    z-index: 3;
    animation: fadeIn 1.5s ease-in-out;
 }

 @keyframes fadeIn {
  0% {
    opacity: 0;
  }
  100% {
    opacity: 1;
  }
}

 .close {
  position: absolute;
  top: 5%;
  right: 5%;
  cursor: pointer;
 }
</style>
