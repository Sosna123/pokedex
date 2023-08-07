<template>
	<div id="container">
		<h1 id="title">Poke-search</h1>

		<!-- * input and button -->
		<div id="form">
			<input type="text" placeholder="Type in pokemon name/index" v-model="search">
			<!-- * search & random -->
			<br>
			<button @click="updateData()">search</button>
			<button @click="search = Math.trunc(Math.random() * 1010); updateData()">random</button>

			<!-- * previous & next -->
			<br>
			<button @click="prevPokemon(id)">previous</button>
			<button @click="nextPokemon(id)">next</button>
		</div>

		<!-- * pokemon stats -->
		<div id="pokemon-stats" v-if="dataFetched">
			
			<span>
				<h1 id="name-display">{{ name }}</h1>
				<h2 id="id-display">#{{ id }}</h2>
			</span>
			<img :src="imgSrc" alt="pokemon image">
			
			<!-- * If pokemon has 2 types -->
			<div v-if="type2">
				<p class="type">{{ type1 }} / {{ type2 }}</p> 
			</div>
			<div v-else>
				<p class="type">{{ type1 }}</p> 
			</div>

			<p>Stats:</p>
			<div v-for="stat in stats" :key='stat'>
				<p>base {{ stat.stat.name }} - {{ stat.base_stat }}</p>
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
		let stats = ref([])

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

				stats.value = [];
				data.stats.forEach((stat) => {
					stats.value.push(stat)
				})

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
			search, dataFetched, updateData, nextPokemon, prevPokemon,
			name, id, type1, type2, imgSrc, stats
		}
	}
}
</script>

<style>
/* ! body itp. */
body{
	background: black;
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
	background: rgba(255, 255, 255, 0.2);
	width: 600px; 
	height: 1000px;
	border-radius: 40px;
	padding: 25px;
	margin: auto;
}

/* ! content */
p, h1, h2, h3, h4, h5, h6, button{
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
	background-color: rgba(255, 255, 255, 0.5);
	border-radius: 5px;
	padding: 5px;
}

h1#name-display{
	text-transform: capitalize;
	font-size: 32px;
}
h2#id-display{
	font-size: 16px;
}
</style>