<template>
  <div class="home">
    <div class="call-to-action">
      <p>Welcome to book Matcher: We will show you two books and one description. Your job is drag the description to the correct book. Are you a real book matcher?</p>
<router-link class="start-button" to="/playing">Start</router-link>
  </div>
  </div>
</template>
<style scoped>
.home {
  display: grid;
  grid-template-columns: 1fr 1fr 1fr;
}
.call-to-action{
  grid-column-start: 2;
  display: flex;
  flex-direction: column;
  height: 100vh;
  justify-content: center;
  align-content: center;
}
.start-button {
  display: inline-block;
  padding: 0.35em 1.2em;
  background-color: var(--contrast);
}
</style>