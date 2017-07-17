<template lang="pug">
	.box_center.box_white_form
		el-form(:model="addFormSubsidiary" :rules="rules" ref="addFormSubsidiary" v-loading="loading")
			el-row
				el-col(:xs="24" :sm="12" :lg="15")
					el-row.col_form_16.padding_form(:gutter="6")
						el-col(:span="24")
							i.icon.icon-localidad-2
						el-col(:xs="24" :sm="24" :lg="12")
							el-form-item(label="Nombre" prop="name")
								el-input(placeholder="Nombre" v-model="addFormSubsidiary.name")
								span.base_border
							el-form-item(label="Provincia" prop="province_id" v-loading="loading")
								el-select(@change="changeProvince" placeholder="Selecciona" v-model="addFormSubsidiary.province_id")
									el-option(
										v-for="item in provinceApi"
										:key="item.province"
										:label="item.province"
										:value="item.province_id")
								span.base_border
							el-form-item(label="Dirección" prop="address")
								el-input(placeholder="Escribe dirección" v-model="addFormSubsidiary.address")
								span.base_border
						el-col(:xs="24" :sm="24" :lg="12")
							el-form-item(label="Departamento" prop="department_id")
								el-select(@change="changeDepartament" placeholder="Selecciona" v-model="addFormSubsidiary.department_id")
									el-option(
										v-for="item in departamentsApi"
										:key="item.department"
										:label="item.department"
										:value="item.department_id")
								span.base_border
							el-form-item(label="Distrito" prop="district_id" v-loading="loading")
								el-select(placeholder="Selecciona" v-model="addFormSubsidiary.district_id")
									el-option(
										v-for="item in districtsApi"
										:key="item.district"
										:label="item.district"
										:value="item.district_id")
								span.base_border
							el-form-item(label="Número o Referencia" prop="reference")
								el-input(placeholder="Número o Referencia" v-model="addFormSubsidiary.reference")
								span.base_border
				el-col.padding_form(:xs="24" :sm="12" :lg="9")
					el-col(:span="24")
							i.icon.icon-user-2
					el-col(:span="24")
						el-form-item(label="Contacto" prop="contact_name")
							el-input(placeholder="Contacto" v-model="addFormSubsidiary.contact_name")
							span.base_border
						el-form-item(label="Cargo" prop="contact_position")
							el-input(placeholder="Cargo" v-model="addFormSubsidiary.contact_position")
							span.base_border
						el-col.padding_right.margin_top(:span="12")
							el-form-item(label="Teléfono" prop="contact_phone")
								el-input(placeholder="Teléfono" v-model.number="addFormSubsidiary.contact_phone")
								span.base_border
						el-col.padding_left.margin_top(:span="12")
							el-form-item(label="Celular" prop="contact_cell_phone")
								el-input(placeholder="Celular" v-model.number="addFormSubsidiary.contact_cell_phone")
								span.base_border
						el-col.margin_top(:span="24")
							el-form-item(label="Email" prop="contact_email")
								el-input(type="text" placeholder="Email" v-model="addFormSubsidiary.contact_email")
								span.base_border
					el-col(:span="24")
						el-row(:gutter="6")
							el-col(:span="12")
								el-form-item
									el-button.btn.btn_green(type="button" @click="addSubsidiary('addFormSubsidiary')") Guardar
							el-col(:span="12")
								el-form-item
									router-link(to="/")
										el-button.btn.btn_red(type="button") Cancelar
</template>

<script>
	import axios from 'axios'
	import config from '../../../config'

	export default {
		name: 'FormSubsidiary',
		mounted () {
			config.domain= (config.isProd) ? location.origin : config.domain;
			this.loading= true;
			this.getDepartaments(()=> {
				this.loading= false;
				if(this.idSubsidiary != 0) {
					this.populateForm();
				}
			});
			document.addEventListener("keydown", (e) => {
				if(e.keyCode == 13) {
					this.addSubsidiary()
					e.preventDefault()
				}
			});
		},
		data() {
			let idSubsidiary= (this.$route.params && this.$route.params.id)? this.$route.params.id : 0;
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
				addFormSubsidiary:{
					name: '',
					department_id: '',
					province_id: '',
					district_id: '',
					address: '',
					reference:'',
					contact_name: '',
					contact_position: '',
					contact_phone: null,
					contact_cell_phone: null,
					contact_email: ''
				},
				idSubsidiary: idSubsidiary,
				departamentsApi:null,
				provinceApi:null,
				districtsApi:null,
				tmpProvince: false,
				tmpDistrict: false,
				loading : false,
				rules: {
					name: [
						{ required: true, message: 'El nombre es requerido', trigger: 'blur' },
						{ min: 3, max: 25, message: 'Debe ser mayor o igual a 3 letras', trigger: 'blur' }
					],
					department_id:[
						{type:'number', required: true, message: 'Elija un departamento', trigger: 'change'}
					],
					province_id: [
						{ type:'number', required: true, message: 'Elija una provincia', trigger: 'change' },
					],
					district_id: [
						{ type:'number', required: true, message: 'Elija un distrito', trigger: 'change' }
					],
					address:[
						{ required: true, message: 'La dirección es requerida', trigger: 'blur' }
					],
					reference:[
						{ required: true, message: 'La referencia es requerida', trigger: 'blur' }
					],
					contact_name:[
						{ required: true, message: 'El nombre del contacto es requerido', trigger: 'blur' }
					],
					contact_position:[
						{ required: true, message: 'El cargo es requerido', trigger: 'blur' }
					],
					contact_phone:[
						 { required: true, validator: checkNumber, trigger: 'blur' }
					],
					contact_cell_phone:[
						 { required: true, validator: checkNumber, trigger: 'blur' }
					],
					contact_email:[
						{ required: true, message: 'El email es requerido', trigger: 'blur' },
						{ type: 'email', message: 'Email inválido', trigger: 'blur' }
					]
				}
			}
		},
		methods: {
			getDepartaments(callback){
				let self= this;
				axios.get(config.domain + '/empresa/ubigeo/city')
					.then(
						(response) => {
							if(response.data.state != 0){
								this.departamentsApi = response.data.data
							}else{
								console.log(response.data.msg)
							}
							callback && callback(response);
						}
					).catch((error) => {
						self.loading= false;
						console.log(error);
					})
			},
			changeDepartament(selectedDepa){
				this.addFormSubsidiary.province_id = ''
				this.addFormSubsidiary.district_id = ''
				this.loading = true
				axios.get(config.domain + '/empresa/ubigeo/province?department_id='+ selectedDepa)
					.then(
						(response) => {
							if(response.data.state != 0){
								this.provinceApi = response.data.data
								if(this.tmpProvince !== false){
									this.addFormSubsidiary.province_id= this.tmpProvince;
									this.tmpProvince= false;
								}
							}
							this.loading = false
						}
					).catch((error) => {
							this.loading = false
							console.log(error);
					})
			},
			changeProvince(selectedProvince){
				this.addFormSubsidiary.district_id = ''
				var selectedDepa = this.addFormSubsidiary.department_id
				this.loading = true
				axios.get(config.domain + '/empresa/ubigeo/district?department_id='+ selectedDepa + '&province_id='+ selectedProvince)
					.then(
						(response) => {
							if(response.data.state != 0){
								this.districtsApi = response.data.data
								if(this.tmpDistrict !== false){
									this.addFormSubsidiary.district_id= this.tmpDistrict;
									this.tmpDistrict= false;
								}
							}
							this.loading = false
						}
					).catch((error) => {
							this.loading = false
							console.log(error);
					})
			},
			addSubsidiary(formName){
				var router = this.$router;
				var params = this.addFormSubsidiary;
				var addParam= (this.idSubsidiary != 0)? '/' + this.idSubsidiary : '';
				var method= (this.idSubsidiary != 0)? 'PUT'  : 'POST';
				this.$refs.addFormSubsidiary.validate((valid) => {
					if (valid) {
						this.loading= true;
						axios({method: method, url: config.domain + '/empresa/establishment' + addParam, data: params})
							.then((response)=>{
								if(response.data.state == 1){
									router.push('/');
								}else{
									console.log(response.data)
								}
								this.loading= false;
							})
							.catch((error)=>{
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
				let self= this;
				this.loading= true;
				axios.get(config.domain + '/empresa/establishment/' + this.idSubsidiary)
					.then((response) => {
						self.loading= false;
						if(parseInt(response.data.state) != 0){
							self.populateModel(response.data.data, ['province_id', 'district_id']);
							this.tmpProvince= response.data.data.province_id;
							this.tmpDistrict= response.data.data.district_id;
						}else{
							console.log(response.data.msg);
						}
					}).catch((error)=> {
						self.loading= false;
						console.log(error);
					});
			},
			populateModel(data, ignore){
				let arrIgnore= (typeof ignore!='undefined')? ignore : [];
				for (var index in data) {
					if(typeof this.addFormSubsidiary[index] != "undefined" && arrIgnore.indexOf(index) == -1){
						this.addFormSubsidiary[index]= data[index];
					}
				}
			}
		}
	}
</script>
<style lang="stylus" scoped>
.padding_right
	padding-right 3px
.padding_left
	padding-left 3px
.margin_top
	margin -30px 0 0 0
.padding_form
	margin-right 0 !important
</style>
