<!-- A box that watches for being in viewport -->

<template lang="pug">

.box: div
	h1: span Box {{ index }}
	p {{ lorem }} 

</template>

<!-- ––––––––––––––––––––––––––––––––––––––––––––––––––––––––––––––––––––––– -->

<script lang="coffee">
import { LoremIpsum } from "lorem-ipsum"
export default

	props: index: Number

	# Make lorem ipsum duing SSR
	data: -> lorem: null
	fetch: -> @lorem = (new LoremIpsum).generateParagraphs(1)

	# Restart below the fold elements when they are in viewport
	mounted: ->
		if @isBelowViewport()
			@resetAnimations()
			@whenFirstInViewport @playAnimations

	methods:

		# Check if in viewport right now
		isBelowViewport: -> @$el.offsetTop > window.innerHeight

		# Restart all of the animations inside the container
		resetAnimations: ->
			for animation in @$el.getAnimations subtree: true
				animation.pause()
				animation.currentTime = 0

		# Invoke callback when in viewport
		whenFirstInViewport: (callback) ->
			observer = new IntersectionObserver ([ entry ]) ->
				return unless entry.isIntersecting
				callback()
				observer.disconnect()
			observer.observe @$el

		# Play all the animations
		playAnimations: ->
			for animation in @$el.getAnimations subtree: true
				animation.play()
</script>

<!-- ––––––––––––––––––––––––––––––––––––––––––––––––––––––––––––––––––––––– -->

<style lang="stylus" scoped>

// Make box shape
.box
	flex-v-center()
	min-height (100vh / 3)
	border 1px solid currentColor
	padding 2em

	// Push boxes apart
	margin-bottom 5px

	// Fade in background a bit
	@keyframes box-intro
		to
			box-shadow 0 0 20px secondary-color
	animation box-intro 3s both

// Zoom in the title
h1 span
	display inline-block
	@keyframes h1-intro
		from
			opacity 0
			transform scale(1.5) translateY(10px)
	animation h1-intro 1s ease-out-quint both

// Slide up the description
p
	margin-bottom 0
	@keyframes p-intro
		from
			opacity 0
			transform translateY(10px)
	animation p-intro 1s .5s ease-out both
</style>
