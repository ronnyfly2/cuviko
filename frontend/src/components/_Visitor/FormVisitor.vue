<template lang="pug">
	.box_center.box_white_form
		el-form(:model="form" :rules="rules" ref="form" v-loading="loading")
			el-row
				el-col(:span="15")
					el-row.col_form_16.padding_form(:gutter="6")
						el-col(:span="24")
							i.icon.icon-localidad-2
						el-col(:span="24" style="margin:0")
							el-form-item(label="Nombre" prop="fullname" style="margin-bottom:0")
								el-input(placeholder="Nombre" v-model="form.fullname")
								span.base_border
						el-col(:span="12")
							el-form-item(label="Correo" prop="email")
								el-input(placeholder="Correo" v-model="form.email")
								span.base_border
							el-form-item(label="Tipo de Documento" prop="document_id")
								el-select(placeholder="Selecciona" v-model="form.document_id")
									el-option(v-for="item in documentApi" :key="item.id" :label="item.name" :value="item.id")
								span.base_border
							el-form-item(label="Contraseña" prop="password")
								el-input(type="password" placeholder="Contraseña" v-model="form.password")
								span.base_border
						el-col(:span="12")
							el-form-item(label="Teléfono" prop="phone")
								el-input(placeholder="Teléfono" v-model="form.phone")
								span.base_border
							el-form-item(label="Número de Documento" prop="document_number")
								el-input(placeholder="Número de Documento" v-model="form.document_number")
								span.base_border
							el-form-item(label="Repetir Contraseña" prop="password_repeat")
								el-input(type="password" placeholder="Repetir Contraseña" v-model="form.password_repeat")
								span.base_border
				el-col.padding_form(:span="9")
					el-col(:span="24")
							i.icon.icon-user-2
					el-col(:span="24")
						el-form-item(label="Sucursal" prop="establishment_id")
							el-select(placeholder="Selecciona" v-model="form.establishment_id")
								el-option(v-for="item in subsidiaryApi" :key="item.id" :label="item.name" :value="item.id")
							span.base_border
						el-form-item(label="Número o nombre de inmueble" prop="property")
							el-input(placeholder="Número o nombre de inmueble" v-model="form.property")
							span.base_border
						el-form-item(label="Bloque" prop="block")
							el-input(placeholder="Bloque" v-model="form.block")
							span.base_border
			el-row.row_btn(:gutter="60")
				el-col(:span="12")
					fieldset
						button.btn.btn_green(type="button" @click="addVisitor") Guardar
				el-col(:span="12")
					fieldset
						button.btn.btn_red(type="button") Cancelar


</template>

<script>
	import axios from 'axios'
	import configForm from '../../forms/visitor'
	import config from '../../../config'

	export default {
		name: 'FormVisitor',
		mounted () {
			config.domain= (config.isProd) ? location.origin : config.domain;
			this.loading= true;
			this.getTypeDocument();
			this.getSubsidiary(()=> {
				this.loading= false;
				if(this.idAgent != 0) {
					this.populateForm();
				}
			});
			document.addEventListener("keydown", (e) => {
				if(e.keyCode == 13) {
					this.addVisitor()
					e.preventDefault()
				}
			});
		},
		data() {
			let objConfigForm= JSON.parse(JSON.stringify(configForm));
			let idVisitor= (this.$route.params && this.$route.params.id)? this.$route.params.id : 0;
			return {
				idVisitor: idVisitor,
				form: objConfigForm.form,
				rules: objConfigForm.rules,
				documentApi: null,
				subsidiaryApi: null,
				loading: false
			}
		},
		methods: {
			getTypeDocument(){
				axios.get(config.domain + '/empresa/document').then((response) => {
					if(parseFloat(response.data.state) != 0){
						this.documentApi = response.data.data
					}
				}).catch((error) => {
						this.loading= false;
					console.log(error);
				})
			},
			getSubsidiary(callback){
				let self= this;
				axios.get(config.domain + '/empresa/establishment').then((response) => {
					if(parseFloat(response.data.state) != 0){
						this.subsidiaryApi = response.data.data
					}
					callback && callback(response);
				}).catch((error) => {
					self.loading= false;
					console.log(error);
				})
			},
			addVisitor(){
				var router = this.$router;
				var params = this.form;
				var addParam= (this.idVisitor != 0)? '/' + this.idVisitor : '';
				var method= (this.idVisitor != 0)? 'PUT'  : 'POST';
				this.$refs.form.validate((valid) => {
					if (valid) {
						this.loading= true;
						axios({method: method, url: config.domain + '/empresa/client' + addParam, data: params}).then((response)=>{
							if(response.data.state == 1){
								router.push('/visitor');
								this.$notify({
									title: 'Success',
									message: response.data.msg,
									type: 'success'
								});
							}else{
								console.log(response.data)
							}
							this.loading= false;
						}).catch((error)=>{
								this.loading= false;
							console.log(error);
						})
					} else {
						console.log('error submit!!');
						return false;
					}
				});
			},
			populateForm(){
				let self = this;
				this.loading = true;
				axios.get(config.domain + '/empresa/client/' + this.idVisitor).then((response) => {
					self.loading = false;
					if (parseInt(response.data.state) != 0) {
						self.populateModel(response.data.data);
					} else {
						console.log(response.data.msg);
					}
				}).catch((error) => {
					self.loading = false;
					console.log(error);
				});
			},
			populateModel(data, ignore){
				let arrIgnore = (typeof ignore != 'undefined') ? ignore : [];
				for (var index in data) {
					if (typeof this.form[index] != "undefined" && arrIgnore.indexOf(index) == -1) {
						this.form[index] = data[index];
					}
				}
			}
		}
	}
</script>

<style lang="stylus">
	.box_center
		&.box_white_form
			fieldset
				margin 33px 0
			.row_btn
				margin-top 20px
				.btn
					max-width 185px
				fieldset
					position relative
					.btn_green
						position absolute
						right 0
	</style>
