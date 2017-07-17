<template lang="pug">
	.box_center.list_normal
		el-row.list_source
			el-col(:span="7")
				router-link.btn.btn_green.btn_create(to="/agente-form") + Crear Agente
		el-row.empty_padding(justify="space-between" :gutter="20" v-loading="loading")
			el-col(:xs="24" :sm="12" :lg="8" v-for="item in listVigilant" :key="item.id")
				.grid_content_security_agent
					.img_box
						img(:src="item.photo")
					.box_security_agent
						.box_item
							.box_text
								h4 {{ item.names }}
								span.id_sub {{ item.company }}
						hr
						.box_item.box_item_gray
							.box_text
								h4 {{ item.establishment }}
								span.position {{ item.cabin }}
						.mask
							router-link(:to="{name: 'FormAgent', params: {'id': item.id}}")
								i.icon.icon-edit
								span Editar
							//a(href="javascript:;")
								//i.icon.icon-eye-2
								//span Detalle
							a(href="javascript:;" @click="deleteAgent(item.id)")
								i.icon.icon-trash-2
								span Eliminar
</template>

<script>
import axios from 'axios'
import config from '../../../config'

export default {
	name: 'securityagent',
	mounted () {
		config.domain= (config.isProd) ? location.origin : config.domain;
		this.getAgent()
	},
	data () {
		return {
			listVigilant: null,
			loading: false
		}
	},
	methods: {
		getAgent(){
			this.loading = true

			axios.get(config.domain + '/empresa/vigilant').then((response) => {
				if(response.data.state != 0){
					this.listVigilant = response.data.data
					this.loading = false
				}else{
					console.log(response.data.msg)
				}
			}).catch((error) => {
					console.log(error);
			});
		},
		deleteAgent(id){
			this.$confirm('¿Desea eliminar el agente de seguridad seleccionado?', 'Warning', {
				confirmButtonText: 'OK',
				cancelButtonText: 'Cancelar',
				type: 'warning'
			}).then(() => {
				this.loading= true;
				axios({method: 'DELETE', url: config.domain + '/empresa/vigilant/' + id}).then((response)=>{
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
<style lang="stylus">
.grid_content_security_agent
	border-radius 5px
	background white
	height 171px
	box-shadow 0 4px 7px 0 rgba(191, 231, 255, 0.6)
	padding 34px 22px
	text-align left
	position relative
	min-width 390px
	i
		font-size 28px
		margin-right 22px
	hr
		border-top 1px solid #bed2ee
		margin 5px 0
	.mask
		display none
		padding-top 60px
		text-align center
	&:hover
		.mask
			display block
	.img_box
	.box_security_agent
		display inline-block
		vertical-align top
	.img_box
		max-width 88px
		max-height 88px
		border-radius 50%
		overflow hidden
		margin-right 3.853%
		width 22.604%
		height 100%
		img
			margin-left -50%
			margin-top -50%
	.box_security_agent
		width 73.55%
</style>
