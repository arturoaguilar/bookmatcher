<template>
  <div class="end__message">
    <p>Thank you for playing  <b>{{ name }}</b>.</p>
    <p>Your wont <b>{{ points }}</b> points</p>
    <button class="acept-button" @click="playAgain">Cool!</button>
  </div>
</template>
<script>
import { useRouter } from "vue-router";

export default {
  name: "End",
  props: ["name", "points"],
  setup() {
    const router = useRouter();

    function playAgain(){
        console.log("Ya se terminó");
      router.push('/');
    }
    return {
      playAgain,
    };
  },
};
</script>
<style scoped>
.end__message {
  padding: 16px;
  background-color: var(--white);
  color: var(--black);
}
.acept-button {
    margin-block: 16px;
  border: solid 1px var(--contrast);
  display: inline-block;
  padding: 0.35em 1.2em;
  background-color: var(--contrast);
  color: var(--white);
  font-size: 1.3rem;
}
</style>