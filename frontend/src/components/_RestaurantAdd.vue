<template lang="pug">
	.restaurante-add
		h2 {{msg}}
		form(v-on:submit.prevent="saveRestaurant")
			<p>
				<label>Nombre</label>
				<input type="text" v-model="restaurante.nombre">
			</p>
			<p>
				<label>Dirección</label>
				<input type="text" v-model="restaurante.direccion">
			</p>
			<p>
				<label>imagen</label>
				<input type="text" v-model="restaurante.imagen">
			</p>
			<p>
				<label>Descripción</label>
				<textarea v-model="restaurante.descripcion"></textarea>
			</p>
			<p>
				<label>Precio</label>
				<select v-model="restaurante.precio">
					<option value="">Selecciona una opción...</option>
					<option value="bajo">Bajo</option>
					<option value="normal">Normal</option>
					<option value="alto">Alto</option>
				</select>
			</p>
			<input type="submit" value="Guardar restaurante">
		</form>
	</div>
</template>

<script>
import axios from 'axios'
import config from '../../config'

export default {
	name: 'restaurantAdd',
	mounted(){

	},
	data(){
		return {
			msg: 'Vue, say Add',
			restaurante: {
				nombre: '',
				direccion: '',
				descripcion: '',
				precio: 'normal'
			}
		}
	},
	methods: {
		saveRestaurant(){
			var router = this.$router;
			var config = {
				headers: {'X-My-Custom-Header': '*'}
			}
			var params = "json="+JSON.stringify(this.restaurante);
			axios.post(config.domain + '/restaurantes-api.php/restaurantes', params)
						.then((respuesta)=>{

						if(respuesta.data.status == 'success'){
							// redirigir
							router.push('/restaurants');
						}

						})
						.catch((error)=>{
							console.log('ajaaaa',error);
						});
		}
	}
}
</script>
