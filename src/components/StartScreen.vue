<template>
    <div id="start-screen">
        <div id="start-text" v-show="beforeAnimation">לומדת פיצוץ<br> אסטרואידים</div> 
    </div>
</template>

<script>
export default {
    name: "start-screen",
    data() {
        return {
            beforeAnimation: true,
        }
    },
    components: {

    },
    methods: {
        addAnimation() {
             setTimeout(() => {
                document.getElementById("start-text").classList.add("animate");
                this.removeText();
            },2000);
        },
        removeText() {
            setTimeout(() => {
                this.beforeAnimation = false;
                this.$emit("switch-screen", 2)
            },2500);
        }
    },
    computed: { 
    },
    created() {
        this.addAnimation();
    }
}
</script>

<style scoped>

@keyframes example {
  from {
    font-size: 6vmax;
     opacity: 1;
    }
  to {
    font-size: 0.001vmax;
     opacity: 0;
    }
}

#start-screen {
    display: flex;
    align-items: center;
    justify-content: center;
    width: 100vw;
    height: 100vh;
    background-image: url(../assets/media/startScreen/space-bg.png);
    background-size: 100vw 100vh;
	background-repeat: no-repeat;
}

#start-text {
    position: absolute;
    text-align: center;
    font-family: "amnon";
    font-size: 6vmax;
    color: rgb(255, 208, 0);
}

.animate {
    animation: example 3s linear;
}

</style>

