<template>
  <div id='group_title'>Welcome to Mission-Possible</div>

  <div id='app'><router-view></router-view></div>
</template>

<script>

export default {
  name: 'App'
}

</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}

#group_title {
  text-align: center;
  font-size: 24px;
  margin-bottom: 15px;
}
</style>


