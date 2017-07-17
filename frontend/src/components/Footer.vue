<template lang="pug">
footer
	span Desarrollado por
	img(src="../assets/img/logo_cuviko.png")
</template>

<script>
export default {
	props:['Footer']
}
</script>
<style lang="stylus">
footer
	width 100%
	height 150px
	padding 21px 0
	background-color #009edb
	text-align center
	display block
	margin-top 45px
	span
		color white
		padding-bottom 6px
		display block
</style>
