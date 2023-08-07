<template>
	<div id="container">
		<h1 id="title">Pokedex</h1>

		<!-- * input and button -->
		<div id="form">
			<input type="text" placeholder="Type in pokemon name/index" v-model="search">
			<!-- search & random -->
			<br>
			<button @click="updateData()">search</button>
			<button @click="search = Math.trunc(Math.random() * 1010); updateData()">random</button>

			<!-- previous & next -->
			<br>
			<button @click="prevPokemon(id)">previous</button>
			<button @click="nextPokemon(id)">next</button>
		</div>

		<!-- * pokemon stats -->
		<div id="pokemon-stats" v-if="dataFetched">
			<div id="image" v-if="!showShinyImage">
				<img :src="imgSrc" alt="pokemon image">
				<p>normal</p>
			</div>
			<div id="shinyImage" v-else>
				<img :src="imgSrcShiny" alt="pokemon image">
				<p>shiny</p>
			</div>
			<button @click="showShinyImage = !showShinyImage">change image</button>
			
			<p>Pokemon name: {{ name }}</p>
			<p>Pokemon id: {{ id }}</p>
			
			<!-- * If pokemon has 2 types -->
			<div v-if="type2">
				<p class="type">Pokemon types: {{ type1 }} / {{ type2 }}</p> 
			</div>
			<div v-else>
				<p class="type">Pokemon types: {{ type1 }}</p> 
			</div>
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
		let pastSearch = ref('')
		let dataFetched = ref(false)
		let showShinyImage = ref(false)
		
		// * pokemon stats
		let name = ref(null)
		let id = ref(null)
		let type1 = ref(null)
		let type2 = ref(null)
		let imgSrc = ref(null)
		let imgSrcShiny = ref(null)

		// * fetching data
		const fetchData = async () => {
			if(search.value == ''){
				return null;
			}
			dataFetched.value = false;
			const promise = await fetch(`https://pokeapi.co/api/v2/pokemon/${search.value}`);
			try{
				const data = await promise.json();
				pastSearch.value = search.value;
				search.value = '';
				return data;
			}catch{
				return false;
			}
		}

		// * using fetched data
		const updateData = () => {
			fetchData().then((data) => {
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
			})
		}

		const nextPokemon = (id) => {
			search.value = id + 1;
			updateData()
			search.value = '';
		}

		const prevPokemon = (id) => {
			search.value = id - 1;
			updateData()
			search.value = '';
		}

		onMounted(() => {
			search.value = Math.trunc(Math.random() * 1010)
			updateData()
			search.value = ''
		})

		return{
			search, showShinyImage, dataFetched, updateData, nextPokemon, prevPokemon,
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
	width: 600px; 
	height: 600px;
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

div#form button, div#pokemon-stats button{
	padding: 10px;
	border-radius: 15px;
	width: 100px;
	text-align: center;
	border: none;
	margin: 2px 2px;
	color: black;
	background: rgb(155, 155, 155);
}

div#form button:hover{ 
	background: rgb(183, 183, 183);
}

div#form button:active{
	background: rgb(211, 211, 211);
}

div#pokemon-stats img{
	object-fit: contain;
}
</style>
