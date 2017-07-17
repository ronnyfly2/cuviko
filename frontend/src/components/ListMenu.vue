<template lang="pug">
	header
		.box_center
			img.logo(src="../assets/img/logo_garita.png")
			.icon.icon-menu(@click="openMenu")
			nav
				ul
					li
						router-link(to="/")
							i.icon.icon-localidad
							span SUCURSALES
					li
						router-link(to="/garita")
							i.icon.icon-block-3
							span GARITAS DE CONTROL
					li
						router-link(to="/agente")
							i.icon.icon-police
							span AGENTES DE SEGURIDAD
					li

						router-link(to="/visitor")
							i.icon.icon-user-3
							span VISITABLES
					li
						router-link(to="/report")
							i.icon.icon-note
							span REPORTE DE VISITAS
					li
						router-link(to="/profile")
							i.icon.icon-user
							span PERFIL
					li
						a(href="/empresa/logout")
							i.icon.icon-logout
							span Salir
							
</template>

<script>
import config from '../../config'

export default {
	props:['ListMenu'],
	name: 'ListoMenu',
	mounted () {
	},
	data () {
		return {
		}
	},
	methods: {
		openMenu(){
			let btnMenu = document.querySelectorAll('header .icon-menu');
			let ctnNav = document.querySelectorAll('header nav');
			if (btnMenu[0].classList.length > 2) {
				btnMenu[0].classList.remove('open')
				ctnNav[0].classList.remove('open')
			} else {
				btnMenu[0].classList.add('open')
				ctnNav[0].classList.add('open')
			}
		}
	}
}
</script>
<style lang="stylus">
@import "../stylus/base/base.styl"
header
	height 100px
	width 100%
	background white
	position fixed
	top 0
	z-index 99
	.box_center
		text-align left
		padding 0 10px
.logo
	width 12.538%
	max-width 168px
	margin-top 25px
	min-width 160px
nav
	width 82%
	text-align right
	float right
	max-width 925px
	min-width 500px
	&:before
		clear both
	ul
		list-style-type none
		padding 0

	li
		display inline-block
		margin 0 1.2%
		position relative
		&:last-child
			margin-right 0
	a
		color #009edb
		text-align center
		height 100px
		padding-top 24px
		display block
		.icon
			color #009edb
			font-size 25px
			display block
			text-align center
		&.router-link-exact-active
			border-bottom 3px solid #009edb
			&:after
				content ''
				width 0
				height 0
				border-right 8px solid transparent
				border-top 8px solid transparent
				border-left 8px solid transparent
				border-bottom 8px solid #009edb
				position absolute
				bottom 0
				left 50%
				margin-left -8px
			span
				font-family 'SanFranciscoDisplay-Bold'
		span
			font-size 17px
.icon-menu
	display none
</style>