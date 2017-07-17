<template lang="pug">
	.box_center.box_white_form
		el-form(:model="form" :rules="rules" ref="form" v-loading="loading")
			el-row
				el-col(:span="15")
					el-row.col_form_16.padding_form(:gutter="6")
						el-col(:span="12")
							div.el-form-item
								el-upload.avatar-uploader(action="/" :show-file-list="false" :before-upload="handleImage")
									img.avatar(v-if="form.photo" :src="form.photo")
									i.el-icon-plus.avatar-uploader-icon(v-else)
							el-form-item(label="Correo" prop="email")
								el-input(placeholder="Correo" v-model="form.email")
								span.base_border
							el-form-item(label="Número de Documento" prop="document_number")
								el-input(placeholder="Número de Documento" v-model.number="form.document_number")
								span.base_border
							el-form-item(label="Repetir Contraseña" prop="password_repeat")
								el-input(type="password" placeholder="Repetir Contraseña" v-model="form.password_repeat")
								span.base_border
						el-col(:span="12")
							el-form-item(label="Nombre" prop="name")
								el-input(placeholder="Nombre" v-model="form.name")
								span.base_border
							el-form-item(label="Apellidos" prop="lastname")
								el-input(placeholder="Apellidos" v-model="form.lastname")
								span.base_border
							el-form-item(label="Tipo de Documento" prop="document_id")
								el-select(placeholder="Selecciona" v-model="form.document_id")
									el-option(v-for="item in documentApi" :key="item.id" :label="item.name" :value="item.id")
								span.base_border
							el-form-item(label="Contraseña" prop="password")
								el-input(type="password" placeholder="Contraseña" v-model="form.password")
								span.base_border
				el-col.padding_form(:span="9")
					el-col(:span="24")
							i.icon.icon-localidad-2
					el-col(:span="24")
						el-form-item(label="Empresa de Servicio" prop="company_service")
							el-input(placeholder="Empresa de Servicio" v-model="form.company_service")
							span.base_border
						el-form-item(label="Sucursal" prop="establishment_id")
							el-select(@change="changeSubsidiary" placeholder="Selecciona" v-model="form.establishment_id")
								el-option(v-for="item in subsidiaryApi" :key="item.id" :label="item.name" :value="item.id")
							span.base_border
						el-form-item(label="Garita" prop="cabin_id")
							el-select(placeholder="Selecciona" v-model="form.cabin_id")
								el-option(v-for="item in garitaApi" :key="item.id" :label="item.name" :value="item.id")
							span.base_border
			el-row.row_btn(:gutter="60")
				el-col(:span="12")
					fieldset
						button.btn.btn_green(type="button" @click="addAgent") Guardar
				el-col(:span="12")
					fieldset
						router-link(to="/agente")
							button.btn.btn_red(type="button") Cancelar


</template>

<script>
	import axios from 'axios'
	import configForm from '../../forms/agent'
	import config from '../../../config'

	export default {
		name: 'FormAgent',
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
					this.addAgent()
					e.preventDefault()
				}
			});
		},
		data() {
			let objConfigForm= JSON.parse(JSON.stringify(configForm));
			let idAgent= (this.$route.params && this.$route.params.id)? this.$route.params.id : 0;
			var checkNumber = (rule, value, callback) => {
				if (!value) {
					return callback(new Error('El campo es requerido'));
				}
				setTimeout(() => {
					if (!Number.isInteger(value)) {
						callback(new Error('Ingrese un número válido'));
					} else {
						callback();
					}
				}, 200);
			};
			return {
				idAgent: idAgent,
				form: objConfigForm.form,
				rules: {
					"name": [
						{ required: true, message: 'El nombre es requerido', trigger: 'blur' },
						{ min: 3,  message: 'Debe ser mayor a 3 letras', trigger: 'blur' }
					],
					"lastname": [
						{ required: true, message: 'El apellido es requerido', trigger: 'blur' },
						{ min: 3,  message: 'Debe ser mayor a 3 letras', trigger: 'blur' }
					],
					"email":[
						{ required: true, message: 'El email es requerido', trigger: 'blur' },
						{ type: 'email', message: 'Email inválido', trigger: 'blur' }
					],
					"document_number": [
						{ required: true, validator: checkNumber, trigger: 'blur' }
					],
					"company_service": [
						{ required: true, message: 'La empresa de servicio es requerido', trigger: 'blur' }
					]
				},
				photoChange: false,
				subsidiaryApi: null,
				garitaApi: null,
				documentApi: null,
				tmpCabin: false,
				loading: false
			}
		},
		methods: {
			handleImage(file){
				let self=this;
				var reader = new FileReader();
				reader.readAsDataURL(file);
				reader.onload = function () {
					self.form.photo=reader.result;
					self.photoChange=true;
				};
				reader.onerror = function (error) {
					console.log('Error: ', error);
				};
				return false
			},
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
			changeSubsidiary(selectedSubsidiary){
				this.form.cabin_id = ''
				this.loading = true
				axios.get(config.domain + '/empresa/cabin/fill/'+ selectedSubsidiary).then((response) => {
					if(response.data.state != 0){
						this.garitaApi = response.data.data
						if(this.tmpCabin !== false){
							this.form.cabin_id= this.tmpCabin;
							this.tmpCabin= false;
						}
					}
					this.loading = false
				}).catch((error) => {
					this.loading = false
					console.log(error);
				})
			},
			addAgent(){
				var router = this.$router;
				var params = this.form;
				var addParam= (this.idAgent != 0)? '/' + this.idAgent : '';
				var method= (this.idAgent != 0)? 'PUT'  : 'POST';
				params.photo= (this.photoChange)? this.form.photo : null;
				this.$refs.form.validate((valid) => {
					if (valid) {
						this.loading= true;
						axios({method: method, url: config.domain + '/empresa/vigilant' + addParam, data: params}).then((response)=>{
							if(response.data.state == 1){
								router.push('/agente');
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
				axios.get(config.domain + '/empresa/vigilant/' + this.idAgent).then((response) => {
					self.loading = false;
					if (parseInt(response.data.state) != 0) {
						self.populateModel(response.data.data, ['cabin_id']);
						this.tmpCabin= response.data.data.cabin_id;
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
		.icon-localidad-2
			display block
.avatar-uploader
	.el-upload
		border 1px dashed #d9d9d9
		border-radius 50%
		cursor pointer
		position relative
		overflow hidden
		&:hover
			border-color #20a0ff
	&-icon
		font-size 28px
		color #8c939d
		width 189px
		height 189px
		line-height 189px
		text-align center
.avatar
	width 189px
	height 189px
	display block

</style>
