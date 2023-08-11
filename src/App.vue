<template>
	<div id="container">
		<!-- * input and button -->
		<div id="form">
			<h1 id="title">Poke-search</h1>

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

		<hr>

		<!-- * pokemon stats -->
		<div id="pokemon-info" v-if="dataFetched"> <!-- dataFetched -->
			<div id="name-id-type">
				<h1 id="name-display">{{ name }}</h1>
				<h2 id="id-display">#{{ id }}</h2>

				<!-- * If pokemon has 2 types -->
				<div v-if="type2">
					<h3 class="type">{{ type1 }} / {{ type2 }}</h3> 
				</div>
				<div v-else>
					<h3 class="type">{{ type1 }}</h3> 
				</div>
				
				<img :src="imgSrc" alt="pokemon image">
			</div>

			<div id="stats">
				<div v-for="stat in stats" :key='stat'>
					<p>Base {{ stat.stat.name }} - {{ stat.base_stat }}</p>
				</div>
			</div>
		</div>

		<!-- * loading -->
		<div id="loading" v-else>
			<div id="loadingIcon"></div>
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
		
		// * pokemon info
		let name = ref(null)
		let id = ref(null)
		let type1 = ref(null)
		let type2 = ref(null)
		let imgSrc = ref(null)
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
				
				// * pokemon info
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
#app{
	width: 100%;
	height: 100%;
	padding: 0px;
	margin: 0px;
}

body{
	background: rgb(100, 255, 100);
	width: 100%;
	height: 100%;
	padding: 0px;
	margin: 0px;
	justify-content: center;
}

*{
	font-family: arial;
}

@keyframes loadingDot{
	from {
		background-color: rgba(255, 255, 255, 1)
	}
	to {
		background-color: rgba(255, 255, 255, 0)
	}
	
} 

/* ! divs */

div#container{
	background: rgb(50, 50, 50);
	width: 600px; 
	height: 800px;
	border-radius: 40px;
	padding: 25px;
	margin: auto;
	position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
}

div#form{
	background: rgba(75, 75, 75, .5);
	padding: 5px;
	border-radius: 20px;
	margin-bottom: 10px;
	text-align: center;
}

div#name-id-type{
	background: rgba(75, 75, 75, .5);
	padding: 5px;
	border-radius: 20px;
	margin-bottom: 10px;
	text-align: center;
}

div#stats{
	display: inline-block;
	background: rgba(75, 75, 75, .5);
	padding: 10px 20px;
	border-radius: 20px;
	width: 220px;
}

div#loading{
	text-align: center;
}

div#loadingIcon{
	margin: 20px auto;
	height: 100px;
	width: 100px;
	background-color: white;
	border-radius: 50px;
	animation: 2s infinite alternate loadingDot;
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

div#form button, div#pokemon-info button{
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

div#pokemon-info img{
	object-fit: contain;
	background-color: rgb(155, 155, 155);
	border-radius: 5px;
	padding: 5px;
	margin-bottom: 20px;
}

div#name-id-type h1{
	text-transform: capitalize;
	font-size: 32px;
	margin-bottom: 0;
	margin-top: 20px;
}

div#name-id-type h2{
	font-size: 24px;
	margin: 0;
}

div#name-id-type h3{
	font-size: 20px;
	margin-top: 0;
}
</style>