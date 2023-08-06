<template>
	<div id="container">
		<h1 id="title">Pokedex</h1>

		<!-- * input and button -->
		<div id="form">
			<input type="text" placeholder="Type in pokemon name/index" v-model="search">
			<br>
			<button @click="updateData(search)">search</button>
			<button @click="updateData(Math.trunc(Math.random() * 100))">random</button>
		</div>

		<!-- * pokemon stats -->
		<div id="pokemon-stats" v-if="dataFetched">
			
			<p>Pokemon name: {{ name }}</p>
			<p>Pokemon id: {{ id }}</p>
			
			<!-- * If pokemon has 2 types -->
			<div v-if="type2">
				<p class="type">Pokemon types: {{ type1 }} / {{ type2 }}</p> 
			</div>
			<div v-else>
				<p class="type">Pokemon types: {{ type1 }}</p> 
			</div>
			<img :src="imgSrc" alt="pokemon image">
			<img :src="imgSrcShiny" alt="pokemon image">
		</div>

		<!-- * loading -->
		<div id="loading" v-else>
			<h2>loading...</h2>
		</div>
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
		
		// * pokemon stats
		let name = ref(null)
		let id = ref(null)
		let type1 = ref(null)
		let type2 = ref(null)
		let imgSrc = ref(null)
		let imgSrcShiny = ref(null)

		// * fetching data
		const fetchData = async (search) => {
			if(search == ''){
				return null;
			}
			dataFetched.value = false;
			const promise = await fetch(`https://pokeapi.co/api/v2/pokemon/${search}`);
			const data = await promise.json();
			return data;
		}

		// * using fetched data
		const updateData = (search) => {
			fetchData(search).then((data) => {
				if(!data){
					return false;
				}
				
				// * pokemon stats
				name.value = data.name
				id.value = data.id
					// * types
				type1.value = data.types[0].type.name
				try{
					type2.value = data.types[1].type.name
				}catch{
					type2.value = null
				}

				imgSrc.value = data.sprites.front_default
				imgSrcShiny.value = data.sprites.front_shiny

				// * other vars
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
			name, id, type1, type2, imgSrc, imgSrcShiny
		}
	}
}
</script>

<style>
/* ! body itp. */
body{
	background: white;
	width: 100%;
	height: 100%;
	padding: 0px;
	margin: 0px;
	justify-content: center;
}

*{
	font-family: Arial;
}

/* ! divs */
div{
	margin: 0px auto;
	text-align: center;
}

div#container{
	background: rgba(0, 0, 0, 0.8);
	width: 500px; 
	height: 500px;
	border-radius: 40px;
	padding: 25px;
	margin: auto;
}

/* ! content */
p, h1, h2, button{
	color: white;
}

h1#title{
	font-size: 50px;
}

div#form input{
	padding: 10px;
	border-radius: 15px;
	width: 200px;
	text-align: center;
	border: lightgrey 2px solid;
	margin: 4px 5px;
	color: black;
}
div#form button{
	padding: 10px;
	border-radius: 15px;
	width: 100px;
	text-align: center;
	border: none;
	margin: 0px 2px;
	color: black;
	background: rgb(155, 155, 155);
}
div#form button:hover{ 
	background: rgb(183, 183, 183);
}
div#form button:active{
	background: rgb(211, 211, 211);
}
</style>
