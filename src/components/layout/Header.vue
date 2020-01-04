<template>
  <header class="header">
    <h1>WELCOME TO CYCLES</h1>
    <div class="nav">
      <router-link to="/" class="link">Current cycle</router-link>
      <router-link to="/past" class="link">Past cycles</router-link>
    </div>
  </header>
</template>

<script>
export default {
  name: 'Header'
}
</script>

<style scoped>
.header {
  background: darkcyan;
  color: black;
  height: 200px;
  padding: 20px;
  font-size: 30px;
}
.nav {
  display: flex;
  flex-direction: column;
}
.link {
  text-decoration: none;
  font-size: 20px;
  color: black;
}
</style>


