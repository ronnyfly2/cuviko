<template lang="pug">
	el-form(:model="dataUser" :rules="rules" ref="dataUser" v-loading="loading_us")
		el-row(:gutter="35")
			el-col(:span="12")
				el-form-item(label="Nombre" prop="contact_name")
					el-input(placeholder="Nombre" v-model="dataUser.contact_name")
					span.base_border
				el-form-item(label="Telefono" prop="contact_phone")
					el-input(placeholder="Telefono" v-model="dataUser.contact_phone")
					span.base_border
			el-col(:span="12")
				el-form-item(label="Cargo" prop="contact_position")
					el-input(placeholder="Cargo" v-model="dataUser.contact_position")
					span.base_border
				el-form-item(label="Celular" prop="contact_cell_phone")
					el-input(placeholder="Celular" v-model="dataUser.contact_cell_phone")
					span.base_border
			el-col(:span="24")
				el-form-item.item_mail(label="Correo" prop="contact_email")
					el-input(placeholder="Correo" v-model="dataUser.contact_email")
					span.base_border
			el-col(:span="24")
				el-form-item.item_button
					el-button.btn.btn_gray(type="button" @click="saveUser('dataUser')") Guardar
</template>
<script>
import axios from 'axios'
import config from '../../../config'
export default {
	name: 'User',
	mounted () {
		config.domain= (config.isProd) ? location.origin : config.domain;
		this.loading_us = true;
		this.getUser();
	},
	data () {
		let dataFormUs = '';
		return {
			dataUser:{
				id: '',
				contact_name: '',
				contact_position: '',
				contact_phone: '',
				contact_cell_phone: '',
				contact_email: ''
			},
			dataFormUs: dataFormUs,
			loading_us: false,
			loading:false,
			rules: {
				name: [
					{ required: true, message: 'El nombre es requerido', trigger: 'blur' },
					{ min: 3, max: 25, message: 'Debe ser mayor o igual a 3 letras', trigger: 'blur' }
				],
			}
		}
	},
	methods: {
		getUser(){
			axios.get(config.domain + '/empresa/profileuser')
				.then(
					(response) => {
						if(response.data.state != 0){
							this.populateModelUser(response.data.data, ['id']);
							this.loading_us = false;
						}else{
							this.dataFormUs = '';
						}
					}
				).catch((error) => {
						this.loading_us = false
						console.log(error);
				})
		},
		populateModelUser(data, ignore){
			let arrIgnore= (typeof ignore!='undefined')? ignore : [];
			for (var index in data) {
				if(typeof this.dataUser[index] != "undefined" && arrIgnore.indexOf(index) == -1){
					this.dataUser[index]= data[index];
				}
			}
		},
		saveUser(){
			this.loading_us = true;
			var router = this.$router;
			var params = this.dataUser;
			axios.post(config.domain + '/empresa/profileuser', params)
				.then(
					(response) => {
						if(response.data.state != 0){
							this.populateModelUser(response.data.data, ['id']);
							this.loading_us = false;
							this.$notify({
								title: 'Success',
								message: 'Tu perfil se guardo correctamente',
								type: 'success'
							});
						}else{
							this.dataFormUs = '';
						}
					}
				).catch((error) => {
						this.loading_us = false
						console.log(error);
				})
		}
	}
}
</script>
