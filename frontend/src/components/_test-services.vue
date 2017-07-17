<template lang="pug">
	ul.restaurant(v-if="restaurantsApi != null")
		li(v-for="restaurant in restaurantsApi")
			strong {{restaurant.nombre}}
	span(v-else) Cargando ... 
</template>

<script>
import axios from 'axios'

export default {
	name: 'restaurant',
	mounted () {
		this.getRestaurants()
	},
	data () {
		return {
			msg: 'Vue, say Restaurant list',
			restaurantsApi: null
		}
	},
	methods: {
		getRestaurants(){
			axios.get('http://local.web-api/restaurantes-api.php/restaurantes').then(
				(api) => {
					this.restaurantsApi = api.data.data
					console.log(api.data.data)
				}
			)
		}
	}
}
</script>