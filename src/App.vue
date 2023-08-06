<template>
	<h1>Pokedex</h1>
	<input type="text" placeholder="Type in pokemon name/index" v-model="search">
	<button @click="updateData(search)">submit</button>
	<p>Pokemon name: {{ name }}</p>
	<p>Pokemon id: {{ id }}</p>
</template>

<script>
import { ref } from 'vue'


export default {
  	name: 'App',
	setup(){
		let search = ref('')
		let name = ref(null)
		let id = ref(null)

		const fetchData = async (search) => {
			const promise = await fetch(`https://pokeapi.co/api/v2/pokemon/${search}`);
			const data = await promise.json();
			return data;
		}

		const updateData = (search) => {
			fetchData(search).then((data) => {
				name.value = data.name
				id.value = data.id
			})
		}

		return{
			search, updateData,
			name, id, 
		}
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
