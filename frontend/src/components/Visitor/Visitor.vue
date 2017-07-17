<template lang="pug">
	.box_center.list_normal
		el-row.list_source
			el-col(:span="7")
				router-link.btn.btn_green.btn_create(to="/visitor-form") + Crear Visitable
		el-row.empty_padding(justify="space-between" :gutter="20" v-loading="loading")
			el-col(:xs="24" :sm="12" :lg="8" v-for="item in listVisitor" :key="item.id")
				.grid_content_visitor
					.box_item
						i.icon.icon-user-2
						.box_text
							h4 {{ item.fullname }}
							span.id_sub {{ item.email }}
							span.id_sub {{ item.phone }}
					hr
					.box_item.box_item_gray
						i.icon.icon-localidad-2
						.box_text
							h4 {{ item.establishment }}
							span.position {{ item.address }}
					.mask
						router-link(:to="{name: 'FormVisitor', params: {'id': item.id}}")
							i.icon.icon-edit
							span Editar
						//-a(href="javascript:;")
							//-i.icon.icon-eye-2
							//-span Detalle
						a(href="javascript:;" @click="deleteVisitor(item.id)")
							i.icon.icon-trash-2
							span Eliminar
</template>


<script>
import axios from 'axios'
import config from '../../../config'

export default {
	name: 'visitor',
	mounted () {
		config.domain= (config.isProd) ? location.origin : config.domain;
		this.getAgent()
	},
	data () {
		return {
			listVisitor: null,
			loading: false
		}
	},
	methods: {
		getAgent(){
			this.loading = true

			axios.get(config.domain + '/empresa/client').then((response) => {
				if(response.data.state != 0){
					this.listVisitor = response.data.data
					this.loading = false
				}else{
					console.log(response.data.msg)
				}
			}).catch((error) => {
					console.log(error);
			});
		},
		deleteVisitor(id){
			this.$confirm('¿Desea eliminar el visitable seleccionado?', 'Warning', {
				confirmButtonText: 'OK',
				cancelButtonText: 'Cancelar',
				type: 'warning'
			}).then(() => {
				this.loading= true;
				axios({method: 'DELETE', url: config.domain + '/empresa/client/' + id}).then((response)=>{
					if(response.data.state == 1){
						this.getAgent();
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
.grid_content_visitor
	border-radius 5px
	background white
	height 191px
	box-shadow 0 4px 7px 0 rgba(191, 231, 255, 0.6)
	padding 27px 22px
	text-align left
	position relative
	min-width 390px
	.id_sub
		display block
	i
		font-size 28px
		margin-right 22px
	hr
		border-top 1px solid #bed2ee
		margin 15px 0
	.mask
		display none
		padding-top 60px
		text-align center
	&:hover
		.mask
			display block
</style>
