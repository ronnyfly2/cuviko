<template lang="pug">
	.box_center.list_normal
		el-row.list_source
			el-col(:span="7")
				router-link.btn.btn_green.btn_create(to="/garita-form") + Crear Garita
		el-row.empty_padding(justify="space-between" v-loading="loading")
			el-col.col_garitas(:xs="24" :sm="12" :lg="4" v-for="item in listGarita" :key="item.id")
				.grid_content_garitas
					.box_item
						i.icon.icon-block-2
						.box_text
							h4 {{ item.name }}
					hr
					.box_item.box_item_gray
						i.icon.icon-phone
						.box_text
							h4 {{ item.phone }}
							span.position {{ item.annexed }}
					.mask
						router-link(:to="{name: 'FormGarita', params: {'id': item.id}}")
							i.icon.icon-edit
							span Editar
						//a(href="javascript:;")
							//i.icon.icon-eye-2
							//span Detalle
						a(href="javascript:;" @click="deleteGarita(item.id)")
							i.icon.icon-trash-2
							span Eliminar
</template>

<script>
import axios from 'axios'
import config from '../../../config'

export default {
	name: 'controlgaritas',
	mounted () {
		config.domain= (config.isProd) ? location.origin : config.domain;
		this.getGarita()
	},
	data () {
		return {
			listGarita: null,
			loading: false
		}
	},
	methods: {
		getGarita(){
			this.loading = true

			axios.get(config.domain + '/empresa/cabin').then((response) => {
				if(response.data.state != 0){
					this.listGarita = response.data.data
					this.loading = false
				}else{
					console.log(response.data.msg)
				}
			}).catch((error) => {
				console.log(error);
			});
		},
		deleteGarita(id){
			this.$confirm('¿Desea eliminar la garita seleccionada?', 'Warning', {
				confirmButtonText: 'OK',
				cancelButtonText: 'Cancelar',
				type: 'warning'
			}).then(() => {
				this.loading= true;
				axios({method: 'DELETE', url: config.domain + '/empresa/cabin/' + id}).then((response)=>{
					if(response.data.state == 1){
						this.getGarita();
						this.$notify({
							title: 'Success',
							message: response.data.msg,
							type: 'success'
						});
					}else{
						this.$notify.error({
							title: 'Error',
							message: response.data.msg
						});
					}
					this.loading= false;
				}).catch((error)=>{
					this.loading= false;
					console.log(error);
				});
			}).catch(()=>{});
		}
	}
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style lang="stylus">
.col_garitas
	height 286px
	padding-top 44px
	margin-bottom 30px
.grid_content_garitas
	object-fit contain
	position relative
	margin 0 auto
	padding 10px 15px
	border-radius 5px
	display inline-block
	position relative
	width 210px
	height 198px
	background-color white
	&:after
	&:before
		content ''
		position absolute
		width 0
		border-left 107px solid transparent
		border-right 107px solid transparent
		left 0
	&:before
		border-radius 10px
		top -42px
		border-bottom 44px solid white
		margin-left -2px
	&:after
		border-radius 10px
		bottom -42px
		width 0
		border-top 44px solid white
		margin-left -2px
	hr
		border solid 0 #bed2ee
		border-top solid 1px #bed2ee
		margin 12px 0
	.mask
		display none
		text-align center
		padding-top 65px
	&:hover
		&:after
			border-top 44px solid rgba(0,72,101,0.91)
		&:before
			border-bottom 44px solid rgba(0,72,101,0.91)
		.mask
			display block
	.box_text
		h4
			display block
			height 42px
			overflow hidden
	.box_item_gray
		h4
			height auto
	.box_item
		i
			font-size 28px
			display block
			&.icon-phone
				font-size 11px
</style>
