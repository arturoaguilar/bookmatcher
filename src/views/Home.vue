<template>
  <div class="home">
    <div class="call-to-action">
      <p>
        Welcome to book Matcher! You have to drag the description to the correct book to win points.
        Enter the user name that you going to use, to start:
      </p>

      <input class="player-name" v-model="playerName" type="text" name="" id="">
<button v-show="playerName !=''" class="start-button" @click="startGame()" > Play !</button>
      <!--<router-link @click="localStorage.setItem('playerName', playerName);" v-show="playerName !=''" class="start-button" to="/playing">Start</router-link>-->
    </div>
  </div>
</template>
<script>
import {ref} from "@vue/reactivity"
import {useRouter} from "vue-router"
export default {
  setup() {
    const router = useRouter();
    const playerName = ref('');

    function startGame(){
      localStorage.setItem('playerName', playerName.value);
      router.push('/playing')
    }
    return {
      playerName,
      startGame
    }
  },
}
</script>
<style scoped>
.home {
  background-color: var(--primary);
  color: var(--white);
  font-size: 1.5rem;
  display: grid;
  grid-template-columns: 1fr 1fr 1fr;
}
.player-name{
  font-size: 1.5rem;
}
.call-to-action {
  grid-column-start: 2;
  display: flex;
  flex-direction: column;
  height: 100vh;
  justify-content: center;
  align-content: center;
  color: var(--white);
}
.start-button {
  margin-block-start: 16px;
  border: solid 1px var(--contrast);
  display: inline-block;
  padding: 0.35em 1.2em;
  background-color: var(--contrast);
  color: var(--white);
  font-size: 1.5rem;
}
</style>