<template>
	<h1>Pokedex</h1>
	<div id="form">
		<input type="text" placeholder="Type in pokemon name/index" v-model="search">
		<button @click="updateData(search)">search</button>
		<button @click="updateData(Math.trunc(Math.random() * 100))">random</button>
	</div>
	
	<div id="pokemon-stats" v-if="dataFetched">
		<p>Pokemon name: {{ name }}</p>
		<p>Pokemon id: {{ id }}</p>

		<!-- If pokemon has 2 types -->
		<div v-if="type2">
			<p class="type">Pokemon types: {{ type1 }} / {{ type2 }}</p> 
		</div>
		<div v-else>
			<p class="type">Pokemon types: {{ type1 }}</p> 
		</div>
	</div>
	<div id="loading" v-else>
		<h2>loading...</h2>
	</div>
	<div id="error" v-if="error">
		<h2>error fetching data...</h2>
	</div>
</template>

<script>
import { onMounted, ref } from 'vue'

export default {
  	name: 'App',
	setup(){
		let search = ref('')
		let dataFetched = ref(false)
		let error = ref(false)
		
		// pokemon stats
		let name = ref(null)
		let id = ref(null)
		let type1 = ref(null)
		let type2 = ref(null)

		// fetching data
		const fetchData = async (search) => {
			if(search == ''){
				return null;
			}
			dataFetched.value = false;
			const promise = await fetch(`https://pokeapi.co/api/v2/pokemon/${search}`);
			const data = await promise.json();
			return data;
		}

		// using fetched data
		const updateData = (search) => {
			fetchData(search).then((data) => {
				if(!data){
					return false;
				}
				name.value = data.name
				id.value = data.id
				type1.value = data.types[0].type.name
				try{
				type2.value = data.types[1].type.name
				}catch{
					type2.value = null
				}
				dataFetched.value = true
				// search.value = ''
			})
		}

		onMounted(() => {
			search.value = Math.trunc(Math.random() * 100)
			updateData(search.value)
			search.value = ''
		})

		return{
			search, error, dataFetched, updateData,
			name, id, type1, type2
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
