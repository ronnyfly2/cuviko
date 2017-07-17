<template lang="pug">
	.login
		.box_login
			img(src="../assets/img/logo_garita.png")
			h3 Bienvenido
			form
				fieldset
					label.mail
					input(type="text" placeholder="Correo")
					span.base_border
				fieldset
					label.pass
					input(type="text" placeholder="Contraseña")
					span.base_border
				fieldset
					button.btn.btn_green(type="submit") Ingresar
				a.forgot_link(href="javascript:;") ¿Olvidaste tu contraseña?
				span ¿Eres nuevo?
				a.new_user_link(href="javascript:;") Registrate
</template>

<script>
export default {
	name: 'login',
	data () {
		return {
			msg: 'Vue, say login'
		}
	}
}
</script>

<style lang="stylus">
@import "../stylus/base/_normalize.styl"
@import "../stylus/base/_reset.styl"
@import "../stylus/base/_icons.styl"
@import "../stylus/base/_fonts.styl"
html
	font-family 'SanFranciscoDisplay-Regular'
	background-color #fafbfc
	-webkit-font-smoothing antialiased
	-moz-osx-font-smoothing grayscale
	font-size 16px
body
	margin 0
	height 100vh
h3
	margin 0
.login
	background white url('../assets/img/fondo.jpg') no-repeat
	background-size cover
	height 100%
	width 100%
.box_login
	object-fit contain
	margin 0 auto
	padding 10px 15px
	border-radius 30px
	display inline-block
	position relative
	margin-top 15%
	width 370px
	height 440px
	box-sizing border-box
	background-color white
	z-index 2
	&:after
	&:before
		content ''
		position absolute
		width 0
		border-left 166px solid transparent
		border-right 166px solid transparent
		left 21px
		z-index -1
	&:before
		border-radius 10px
		top -49px
		border-bottom 51px solid white
		margin-left -1px
	&:after
		border-radius 10px
		bottom -49px
		width 0
		border-top 51px solid white
		margin-left -1px
	.btn
		width 100%
		display block
		outline none
		line-height 1.97
		font-size 20px
		text-align center
		color #ffffff
		border-radius 7px
		border 0
		padding 0
		&.btn_green
			background-color #21d39a
	span
		display block
		font-size 16px
		text-align center
		color #004965
	a
		display block
		font-size 16px
		text-align center
		color #004965
		&:hover
			text-decoration underline
		&.forgot_link
			margin-top -16px
			margin-bottom 30px
		&.new_user_link
			color #009edb
	form
		margin 0 auto
		margin-top 38px
		width 250px
	h3
		color #009edb
		font-family 'SanFranciscoDisplay-Semibold'
		font-size 20px
		margin-top 40px
	label
		width 100%
		background #f4f9ff
		font-size 18px
		color #009edb
		display block
		line-height 2.6
		font-family SanFranciscoDisplay-Bold
	input
		width 96%
		outline 0
		&[type=text]
			color #58889c
			height 35px
			text-align  center
			border 0
			border-bottom 1px solid #bed2ee
			font-size 16px
			border-radius 0
			&::-webkit-input-placeholder
				color #58889c
			&::-moz-placeholder
				color #58889c
				opacity 1
			&:-moz-placeholder
				color #58889c
				opacity 1
			&::-o-input-placeholder
				color #58889c
			&::-ms-input-placeholder
				color #58889c
			&:-ms-input-placeholder
				color #58889c
			&[readonly]
				color #bed2ee
	.el-input
		input
			&[readonly]
				color #58889c
	.el-select
		width 100%
		border 0
		border-radius 0
		background white
		text-align center
		height 35px
		outline 0
		color #58889c
		option
			color #58889c
			text-align center
	.el-form-item__label
		padding 0
		text-align center
	.el-button
		padding 0
		margin -15px 0
	fieldset
	.el-form-item
		margin 30px 0
		position relative
		.base_border
			display block
			width 100%
			&:before
			&:after
				content ''
				width 1px
				background #bed2ee
				height 5px
				position absolute
				bottom 0
			&:after
				right 2%
			&:before
				left 2%
	.icon
		font-size 27px
		color #009edb
</style>