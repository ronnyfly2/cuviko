<template lang="pug">
.box_center.box_white_form.list_report
	el-row
		el-col(:xs="24" :sm="12" :lg="8")
			.download
				el-button(type="primary" icon=" icon icon-arrow-donw" size="large" @click="getExcel") Descargar EXCEL
				el-button(type="primary" icon=" icon icon-arrow-donw" size="large" @click="getPdf") Descargar PDF
		el-col(:xs="24" :sm="12" :lg="8")
			.date
				el-date-picker(v-model="date_report" @change="getDateReport()" placeholder="Filtrar por Fecha")
		el-table(
			:data="tableData"
			border
			stripe
			v-loading="loading")
			el-table-column(
				prop="date_visit"
				label="Fecha y Hora"
				width="140")
				template(scope="scope")
					p {{ scope.row.date_visit }}
					p {{ scope.row.start_time }}
			el-table-column(
				prop="fullname"
				label="Nombre y Apellidos")
			el-table-column(
				prop="document_type"
				label="Documento")
				template(scope="scope")
					p {{ scope.row.document_type }} - {{ scope.row.document_number }}
			el-table-column(
				prop="visited"
				label="Visitó a:")
			el-table-column(
				prop="reason"
				label="Motivo")
				template(scope="scope")
					el-popover(trigger="hover" placement="top")
						p Name: {{ scope.row.reason }}
						div(slot="reference")
							i.icon.icon-eye
			el-table-column(
				prop="state"
				label="Estado")
			el-table-column(
				prop="establishment"
				label="Sucursal")
</template>
<script>
import axios from 'axios'
import config from '../../config'
export default {
	name: 'report',
	mounted () {
		config.domain= (config.isProd) ? location.origin : config.domain;
		this.getListReport();
	},
	data () {
		return {
			loading: false,
			tableData: null,
			date_report: '',
			valDate: ''
		}
	},
	methods: {
		getListReport(){
			this.loading = true;
			axios.get(config.domain + '/empresa/reportvisit?search[date]='+ this.valDate)
				.then(
					(response) => {
						if(response.data.state != 0){
							if(response.data.data.length > 0){
								this.tableData= response.data.data;
								this.loading = false;
							}else{
								this.loading = false;
								this.tableData = null;
								this.$notify.info({
									title: 'No hay',
									message: 'No se encontraron reportes'
								});
							}
						}else{
							this.$notify.info({
								title: 'Error',
								message: 'Error repostado'
							});
						}
					}
				).catch((error) => {
					this.loading = false
					console.log(error);
					this.$notify.error({
						title: 'Error',
						message: 'Ocurrio un error'
					});
				})
		},
		getDateReport(){
			let date = new Date(this.date_report);
			let year = date.getFullYear();
			let month = (date.getMonth() < 10) ? '0' + (date.getMonth() + 1) : (date.getMonth() + 1);
			let day = (date.getDate() < 10) ? '0' + date.getDate() : date.getDate();
			if(this.date_report != ''){
				this.valDate = year + '-' + month + '-' + day;
			}else{
				this.valDate = '';
			}
			this.getListReport()
		},
		getExcel(e){
			let self= this;
			window.location.href = config.domain + '/empresa/reportvisit/excel?search[date]=' + self.valDate;
		},
		getPdf(e){
			let self= this;
			window.location.href = config.domain + '/empresa/reportvisit/pdf?search[date]=' + self.valDate;
		}
	}
}
</script>
<style lang="stylus">
.list_report
	padding 15px
	.el-table
		width 100%
		margin 0 auto
		th
			background-color #ffffff
			>.cell
				font-size 18px
				font-weight 600
				text-align center
				color #009edb
		td
			>.cell
				color #58889c
				font-size 18px
				padding 14px 0
		&__header-wrapper
			thead
				div
					background-color white
		&--striped
			tr
				td
					background #f4f9ff
			.el-table__body
				tr.el-table__row--striped
					td
						background white
	.cell
		.icon
			font-size 12px
			color #009edb
	.download
		padding 15px 0
		text-align left
		.el-button
			line-height 2.4
			padding 0 10px
			margin 0 10px
		.icon
			color white
			font-size 16px
	.date
		padding 17px 0
</style>


