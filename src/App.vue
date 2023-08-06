<template>
	<h1>Pokedex</h1>

	<input type="text" placeholder="Type in pokemon name/index" v-model="search">
</template>

<script>

export default {
  	name: 'App',
	setup(){
		search = 'b'
		apiUrl = `https://pokeapi.co/api/v2/pokemon/${search}`
	}
}
</script>

<style>
	#app {
		font-family: Avenir, Helvetica, Arial, sans-serif;
		-webkit-font-smoothing: antialiased;
		-moz-osx-font-smoothing: grayscale;
		text-align: center;
		color: #2c3e50;
		margin-top: 60px;
	}
</style>
