<script lang="ts">
	import SvelteCarousel from './../lib/SvelteCarousel.svelte';
	let items = [
		{
			src: '/1.webp'
		},
		{
			src: '/2.webp'
		},
		{
			src: '/3.webp'
		},
		{
			src: '/4.webp'
		}
	];
</script>

<div>
	<SvelteCarousel
		{items}
		let:item
		itemToShow={{
			sm: 1,
			md: 2,
			lg: 3,
			xl: 4,
			xxl: 4
		}}
		gap={5}
	>
		<div slot="prevArrow">prev arrow</div>
		<div slot="nextArrow">next arrow</div>
		<img src={item.src} alt={item.src} />
	</SvelteCarousel>
</div>
