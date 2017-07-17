<template lang="pug">
	.box_center.box_white_form.profile
		el-row
			el-col.col_profile(:span="20")
				el-tabs(type="card" v-model="activeName" @tab-click="handleClick")
					el-tab-pane(label="data-user" name="data-user")
						div.tab_user(slot="label")
							i.icon.icon-user-2
							span Datos de Usuario
						User
					el-tab-pane(label="data-business")
						div.tab_user(slot="label")
							i.icon.icon-corbat
							span Datos de la empresa
						el-form(:model="dataBusiness" :rules="rules" ref="dataBusiness" v-loading="loading_bus")
							el-row(:gutter="20")
								el-col(:xs="24" :sm="12" :lg="12")
									el-form-item(label="Nombre de la Empresa" prop="name")
										el-input(placeholder="Nombre de la Empresa" v-model="dataBusiness.name")
										span.base_border
									el-form-item(label="Razón Social" prop="business_name")
										el-input(placeholder="Razón Social" v-model="dataBusiness.business_name")
										span.base_border
									el-row(:gutter="10")
										el-col(:span="12")
											el-form-item(label="País" prop="country")
												el-input(readonly placeholder="País" v-model="dataBusiness.country")
												span.base_border
										el-col(:span="12")
											el-form-item(label="Departamento" prop="department_id")
												el-select(@change="changeDepartament" placeholder="Selecciona" v-model="dataBusiness.department_id")
													el-option(
														v-for="item in departamentsApi"
														:key="item.department"
														:label="item.department"
														:value="item.department_id")
									el-form-item(label="Dirección" prop="address")
										el-input(placeholder="Dirección" v-model="dataBusiness.address")
										span.base_border
								el-col(:xs="24" :sm="12" :lg="12")
									el-form-item(label="RUC" prop="ruc")
												el-input(placeholder="RUC" v-model="dataBusiness.ruc")
												span.base_border
									el-form-item.item_empty(label="") &nbsp;
									el-row(:gutter="10")
										el-col(:span="12")
											el-form-item(label="Provincia" prop="province_id" v-loading="loading")
												el-select(@change="changeProvince" placeholder="Selecciona" v-model="dataBusiness.province_id")
													el-option(
														v-for="item in provinceApi"
														:key="item.province"
														:label="item.province"
														:value="item.province_id")
												span.base_border
										el-col(:span="12")
											el-form-item(label="Distrito" prop="district_id" v-loading="loading")
												el-select(placeholder="Selecciona" v-model="dataBusiness.district_id")
													el-option(
														v-for="item in districtsApi"
														:key="item.district"
														:label="item.district"
														:value="item.district_id")
												span.base_border
									el-form-item(label="Referencia" prop="reference")
										el-input(placeholder="Referencia" v-model="dataBusiness.reference")
										span.base_border
								el-col(:span="24")
									el-form-item.item_button
										el-button.btn.btn_gray(type="button" @click="saveBusiness('dataBusiness')") Guardar
					//-el-tab-pane(label="acount")
					//-	div.tab_user(slot="label")
					//-		i.icon.icon-bille
					//-		span Cuenta
					//-	el-row.row_tbl
					//-		el-col(:span="6")
					//-			span.col_tbl Fecha Último Pago
					//-			span.col_tbl Monto Último Pago
					//-			span.col_tbl Vencimiento
					//-		el-col(:span="6")
					//-			span.col_txt 12/04/17
					//-			span.col_txt 12/04/17
					//-			span.col_txt 12/04/17
					//-		el-col(:span="6")
					//-			span.col_tbl Fecha Mes Actual
					//-			span.col_tbl Monto Mes Actual
					//-			span.col_tbl Estado
					//-		el-col(:span="6")
					//-			span.col_txt 12/04/17
					//-			span.col_txt 12/04/17
					//-			span.col_txt.col_txt_state 12/04/17
					//-	el-row
					//-		el-col(:span="12")
					//-			el-button.acount.btn.btn_green(type="button") Ver Detalle de Monto
					//-		el-col(:span="12")
					//-			el-button.acount.btn.btn_green(type="button") Ver Pagar Mes Actual
					el-tab-pane(label="garita")
						div.tab_user(slot="label")
							i.icon.icon-block-2
							span Garitas y Visitables
						el-row.garitas_vitiables(v-loading="loading")
							el-col(:xs="24" :lg="20")
								el-row(:gutter="5")
									el-col(:span="8")
										span &nbsp;
									el-col.head_col(:span="8")
										span Garitas
									el-col.head_col.head_col_last_child(:span="8")
										span Visitables
									el-col.row_body.col_right(:span="8")
										span Disponibles
									el-col.row_body(:span="8")
										.col_body_right
											a(href="javascript:;" class="rest" @click="decrement('garita')") -
											input(type="text" readonly placeholder="0" v-model="dataCant.cabin_quantity" class="cant")
											a(href="javascript:;" class="plus" @click="aument('garita')") +
									el-col.row_body(:span="8")
										a(href="javascript:;" class="rest" @click="decrement('visitable')") -
										input(type="text" readonly placeholder="0" v-model="dataCant.visitable_quantity" class="cant")
										a(href="javascript:;" class="plus" @click="aument('visitable')") +
									el-col.row_body.col_bottom.col_right(:span="8")
										span En Uso
									el-col.row_body.col_bottom(:span="8")
										.col_body_right
											span Garitas
									el-col.row_body.col_bottom(:span="8")
										span Visitables
								el-col(:span="24")
									el-button.acount.btn.btn_green(type="button" @click="saveGaritas()") Guardar
</template>

<script>
	import axios from 'axios'
	import config from '../../../config'
	import User from './User'

	export default {
		name: 'Profile',
		components: {
			User
		},
		mounted () {
			config.domain= (config.isProd) ? location.origin : config.domain;
			let self= this;
			this.getDepartaments(function(){
				self.getBusiness();
			});
			this.getGaritaVisit()
		},
		data () {
			let tempGaritaCant = null;
			let tempVisitableCant = null;
			return {
				dataBusiness:{
					id: '',
					name: '',
					business_name: '',
					ruc: '',
					country: 'Perú',
					department_id: '',
					province_id: '',
					district_id: '',
					address: '',
					reference: ''
				},
				activeName: 'data-user',
				loading_us: false,
				loading_bus: false,
				loading:false,
				departamentsApi:null,
				provinceApi:null,
				districtsApi:null,
				tmpProvince: false,
				tmpDistrict: false,
				dataCant:{
					cabin_quantity: 0,
					visitable_quantity: 0,
				},
				rules: {
					name: [
						{ required: true, message: 'El nombre es requerido', trigger: 'blur' },
						{ min: 3, max: 25, message: 'Debe ser mayor o igual a 3 letras', trigger: 'blur' }
					],
				}
			}
		},
		methods: {
			handleClick(tab, event) {
				console.log();
			},
			getGaritaVisit(){
				this.loading = true;
				axios.get(config.domain + '/empresa/profilecabinvisit')
					.then(
						(response) => {
							let self = this;
							if(response.data.state != 0){
								self.dataCant = response.data.data;
								this.tempGaritaCant = response.data.data.cabin_quantity;
								this.tempVisitableCant = response.data.data.visitable_quantity;
								this.loading = false;
							}else{
								this.dataCant = '';
								this.loading = false;
							}
						}
					).catch((error) => {
							this.loading = false;
							console.log(error);
					})
			},
			aument(modo){
				if(modo === 'visitable'){
					this.dataCant.visitable_quantity += 1;
				}else{
					this.dataCant.cabin_quantity += 1;
				}
			},
			decrement(modo){
				console.log(this.tempGaritaCant, this.tempVisitableCant);
				switch(modo){
					case 'visitable':
					this.dataCant.visitable_quantity =(this.tempVisitableCant >= this.dataCant.visitable_quantity)?this.dataCant.visitable_quantity :this.dataCant.visitable_quantity-1;
					default :
					this.dataCant.cabin_quantity  =(this.tempGaritaCant >= this.dataCant.cabin_quantity)?this.dataCant.cabin_quantity  :this.dataCant.cabin_quantity -1;
				}
				
			},
			saveGaritas(){
				this.loading = true;
				let params = this.dataCant;
				axios.post(config.domain + '/empresa/profilecabinvisit', params)
					.then(
						(response) => {
							let self = this;
							if(response.data.state != 0){
								self.dataCant = response.data.data;
								this.tempGaritaCant = response.data.data.cabin_quantity;
								this.tempVisitableCant = response.data.data.visitable_quantity;
								this.$notify({
									title: 'Success',
									message: 'Se guardo correctamente!',
									type: 'success'
								});
								this.loading = false;
							}else{
								this.dataCant = '';
								this.loading = false;
							}
						}
					).catch((error) => {
							this.loading = false;
							console.log(error);
					})
			},
			getBusiness(){
				axios.get(config.domain + '/empresa/profilecompany')
					.then(
						(response) => {
							if(response.data.state != 0){
								this.populateModelBussines(response.data.data, ['province_id', 'district_id']);
								this.tmpProvince= response.data.data.province_id;
								this.tmpDistrict= response.data.data.district_id;
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
			populateModelBussines(data, ignore){
				let arrIgnore= (typeof ignore!='undefined')? ignore : [];
				for (var index in data) {
					if(typeof this.dataBusiness[index] != "undefined" && arrIgnore.indexOf(index) == -1){
						this.dataBusiness[index]= data[index];
					}
				}
			},
			saveBusiness(){
				this.loading_bus = true;
				var router = this.$router;
				var params = this.dataBusiness;
				axios.post(config.domain + '/empresa/profilecompany', params)
					.then(
						(response) => {
							if(response.data.state != 0){
								this.populateModelBussines(response.data.data, ['id']);
								this.loading_bus = false;
								this.$notify({
									title: 'Success',
									message: 'Tu Empresa se guardo correctamente',
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
			},
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
				this.dataBusiness.province_id = ''
				this.dataBusiness.district_id = ''
				this.loading = true
				axios.get(config.domain + '/empresa/ubigeo/province?department_id='+ selectedDepa)
					.then(
						(response) => {
							if(response.data.state != 0){
								this.provinceApi = response.data.data
								if(this.tmpProvince !== false){
									this.dataBusiness.province_id= this.tmpProvince;
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
				this.dataBusiness.district_id = ''
				var selectedDepa = this.dataBusiness.department_id
				this.loading = true
				axios.get(config.domain + '/empresa/ubigeo/district?department_id='+ selectedDepa + '&province_id='+ selectedProvince)
					.then(
						(response) => {
							if(response.data.state != 0){
								this.districtsApi = response.data.data
								if(this.tmpDistrict !== false){
									this.dataBusiness.district_id= this.tmpDistrict;
									this.tmpDistrict= false;
								}
							}
							this.loading = false
						}
					).catch((error) => {
							this.loading = false
							console.log(error);
					})
			}
		}
	}
</script>
<style lang="stylus">
.profile
	max-width 1100px
	&:after
	&:before
		content ''
		clear both
	.el-tabs--card>.el-tabs__header
		.el-tabs__item
			border 0
	.col_profile
		margin 0 auto
		float none
	.el-tabs__header
		border-bottom 0
	.el-tabs__nav
		float none
	.el-tabs__item
		color #bed2ee
		height initial
		width 25%
		&:hover
		&.is-active
			border 0 !important
			color #009edb
			.tab_user
				.icon
					color #009edb
				span
					border-bottom solid 3px #009edb
	.item_empty
		height 82px
	.tab_user
		.icon
			display block
			text-align center
			font-size 30px
			color #bed2ee
			&-bille
				font-size 22px
		span
			font-family 'SanFranciscoDisplay-Semibold'
			font-size 18px
	.el-form-item
		&.item_mail
			max-width 440px
			margin 0 auto
		&.item_button
			margin 60px auto 40px auto
			max-width 440px
	.row_tbl
		margin-top 35px
		span
			font-size 18px
			display block
			text-align center
			margin-top 3px
			line-height 2.6
	.col_tbl
		background-color #f4f9ff
		color #009edb
		font-family 'SanFranciscoDisplay-Semibold'
	.col_txt
		color #58889c
		&_state
			color #fd6868
	.el-button
		&.acount
			margin 60px auto 40px auto
			max-width 320px
	.head_col
		&_last_child
			padding-right 0 !important
		span
			display block
			background-color #e4eefc
			font-size 16px
			font-weight 600
			text-align center
			color #009edb
			line-height 2.7
	.row_body
		border-top 1px solid #bed2ee
		padding 8px 0
		&.col_right
			border-right 1px solid #bed2ee
		span
			line-height 1.8
			font-size 16px
			text-align center
			color #58889c
	.col_bottom
		border-bottom 1px solid #bed2ee
	.col_body_right
		border-right 1px solid #bed2ee
	input
		&.cant
			&[type=text]
				border-radius 8px
				display inline-block
				vertical-align middle
				max-width 60px
				background-color #fff
				border 1px solid #bed2ee
				margin 0 8px
				height 28px
				outline 0
	a.plus
	a.rest
		color #009edb
		border-radius 8px
		border 1px solid #bed2ee
		display inline-block
		vertical-align middle
		width 19px
		height 19px
		line-height 1
		background-color #fff
	.garitas_vitiables
		margin-top 35px
</style>
