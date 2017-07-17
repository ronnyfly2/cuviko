<template lang="pug">
	.box_center.box_white_form
		el-form(:model="addFormGaritas" :rules="rules" ref="addFormGaritas" v-loading="loading")
			el-row
				el-col(:span="15")
					el-row.col_form_16.padding_form(:gutter="6")
						el-col(:span="24")
							i.icon.icon-block
						el-col(:span="12")
							el-form-item(label="Nombre de Garita" prop="name")
								el-input(placeholder="Nombre de Garita" v-model="addFormGaritas.name")
								span.base_border
							//-fieldset
								//-label() Identificador de Empresa
								//-el-input(value="CONLAV 2319" v-model="inputRead" readonly)
								//-span
						el-col(:span="12")
							//-fieldset
								//-label() Identificador de Garita
								//-el-input(value="IDK 1313" v-model="inputRead" readonly)
								//-span
							el-form-item(label="Sucursal" prop="establishment_id")
								el-select(placeholder="Selecciona" v-model="addFormGaritas.establishment_id")
									el-option(v-for="item in subsidiaryApi" :key="item.id" :label="item.name" :value="item.id")
								span.base_border
				el-col.padding_form(:span="9")
					el-col(:span="24")
							i.icon.icon-phone
					el-col(:span="24")
						el-form-item(label="Teléfono" prop="phone")
							el-input(placeholder="Teléfono" v-model.number="addFormGaritas.phone")
							span.base_border
						el-form-item(label="Anexo" prop="annexed")
							el-input(placeholder="Anexo" v-model="addFormGaritas.annexed")
							span.base_border
			el-row.row_btn(:gutter="30")
				el-col(:span="12")
					fieldset
						button.btn.btn_green(type="button" @click="addGarita()") Guardar
				el-col(:span="12")
					fieldset
						router-link(to="/garita")
							button.btn.btn_red(type="button") Cancelar
</template>

<script>
	import axios from 'axios'
	import config from '../../../config'

	export default {
		name: 'FormSubsidiary',
		mounted () {
			config.domain= (config.isProd) ? location.origin : config.domain;
			this.loading= true;
			this.getSubsidiary(()=> {
				this.loading= false;
				if(this.idGarita != 0) {
					this.populateForm();
				}
			});
			document.addEventListener("keydown", (e) => {
				if(e.keyCode == 13) {
					this.addGarita()
					e.preventDefault()
				}
			});
		},
		data() {
			let idGarita= (this.$route.params && this.$route.params.id)? this.$route.params.id : 0;
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
				addFormGaritas: {
					name:'',
					phone: '',
					annexed: '',
					establishment_id: ''
				},
				loading: false,
				idGarita: idGarita,
				subsidiaryApi: null,
				rules: {
					name: [
						{ required: true, message: 'El nombre es requerido', trigger: 'blur' }
					],
					phone: [
						{ required: true, validator: checkNumber, trigger: 'blur' }
					],
					annexed: [
						{ required: true, message: 'El anexo es requerido', trigger: 'blur' },
					]
				}
			}
		},
		methods: {
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
			addGarita(){
				var router = this.$router;
				var params = this.addFormGaritas;
				var addParam= (this.idGarita != 0)? '/' + this.idGarita : '';
				var method= (this.idGarita != 0)? 'PUT'  : 'POST';
				this.$refs.addFormGaritas.validate((valid) => {
					if (valid) {
						this.loading= true;
						axios({method: method, url: config.domain + '/empresa/cabin' + addParam, data: params})
							.then((response)=>{
								if(response.data.state == 1){
									router.push('/garita');
									this.$notify({
										title: 'Success',
										message: response.data.msg,
										type: 'success'
									});
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
				axios.get(config.domain + '/empresa/cabin/' + this.idGarita)
					.then((response) => {
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
					if (typeof this.addFormGaritas[index] != "undefined" && arrIgnore.indexOf(index) == -1) {
						this.addFormGaritas[index] = data[index];
					}
				}
			}
		}
	}
</script>
<style lang="stylus">
.box_center
	&.box_white_form
		.icon
			&-phone
				font-size 14px
				height 35px
				display block
</style>
