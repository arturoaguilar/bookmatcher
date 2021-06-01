<template>
  <!--<div id="nav">
    <router-link to="/">Home</router-link> |
    <router-link to="/about">About</router-link>
  </div>-->
  <router-view/>
</template>

<style>
html {
  font-size: 62.5%;
  box-sizing: border-box;
  padding: 0px;
  margin: 0px;
}

:root{
  --contrast:#00C59C;
  --primary:#A4A5FF;
  --secundary:#FFD372;
  --black:#464555;
  --white:#E6F4F1;

}
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}

#nav {
  padding: 30px;
}

#nav a {
  font-weight: bold;
  color: #2c3e50;
}

#nav a.router-link-exact-active {
  color: #42b983;
}
</style>
