<template>
    <div id="icons-for-pou">
        <div id="star">{{ level }}</div>
        <div id="heart">{{ age }}</div>
    </div>
</template>

<script>
export default {
    name:"icons-for-pou",
    props: ["age", "level"],
    data() {
        return {
           
        }
    },
    components: {

    },
    methods: {

    },
    computed: { 
    },
    created() {
        
    }
}
</script>

<style scoped>

 #star {
    display: flex;
    align-items: center;
    justify-content: center;
    position: absolute;
    height: 11vw;
    width: 13vw;
    top: 1.5vw;
    right: 2vw;
    background-image: url(../assets/media/pouScreen/star.png);
    background-size: 100% 100%;
	background-repeat: no-repeat;
 }

  #heart {
    font-size: 2vmax;
    display: flex;
    align-items: center;
    justify-content: center;
    position: absolute;
    top: 1.5vw;
    right: 15vw;
    height: 11vw;
    width: 13vw;
     background-image: url(../assets/media/pouScreen/heart.png);
    background-size: 100% 100%;
	background-repeat: no-repeat;
 }
</style>

