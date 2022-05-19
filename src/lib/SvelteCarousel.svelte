<script lang="ts" context="module">
	export const BREAKPOINTS = {
		sm: 640,
		md: 768,
		lg: 1024,
		xl: 1280,
		xxl: 1440
	};

	export type BreakPoints = typeof BREAKPOINTS;

	export type ScreenSize = keyof BreakPoints;

	export type ValuesWithBreakPoints =
		| number
		| {
				[breakpoint in ScreenSize]: number;
		  };

	export function calculateScreenSize(
		screenWidth: number,
		breakpoints: Partial<BreakPoints>
	): ScreenSize {
		let screenSize: ScreenSize = 'sm';

		if (breakpoints?.sm && screenWidth >= breakpoints.sm) {
			screenSize = 'sm';
		}
		if (breakpoints?.md && screenWidth >= breakpoints.md) {
			screenSize = 'md';
		}
		if (breakpoints?.lg && screenWidth >= breakpoints.lg) {
			screenSize = 'lg';
		}
		if (breakpoints?.xl && screenWidth >= breakpoints.xl) {
			screenSize = 'xl';
		}
		if (breakpoints?.xxl && screenWidth >= breakpoints.xxl) {
			screenSize = 'xxl';
		}

		return screenSize;
	}
</script>

<script lang="ts">
	type T = $$Generic;
	interface $$Slots {
		default: {
			item: T;
			index: number;
		};
	}

	export let items: T[] = [];

	export let breakpoints: BreakPoints = BREAKPOINTS;

	// space between carousel items
	export let gap: ValuesWithBreakPoints = 8;

	// how many items will be shown on screen at the same time
	export let itemToShow: ValuesWithBreakPoints = 1;

	// normalazing itemToShow to proper object annotation
	let normalizedItemToShow =
		typeof itemToShow !== 'number'
			? { ...itemToShow }
			: {
					sm: itemToShow,
					md: itemToShow,
					lg: itemToShow,
					xl: itemToShow,
					xxl: itemToShow
			  };

	// normalazing gap to proper object annotation
	let normalizedGap =
		typeof gap !== 'number'
			? { ...gap }
			: {
					sm: gap * 0.7,
					md: gap * 0.8,
					lg: gap * 0.9,
					xl: gap,
					xxl: gap * 1.1
			  };

	// carouselContainer reference
	let carouselContainer: HTMLDivElement;

	let itemCount = items?.length || 0;

	// current window.innerWidth
	let screenWidth = 0;

	// current breakpoint depends on screenWidth
	$: screenSize = calculateScreenSize(screenWidth, breakpoints);

	// current space between carousel items depends on screenWidth
	$: currentGap = normalizedGap[screenSize];

	// current item count to show depends on screenWidth
	$: currentItemToShow = normalizedItemToShow[screenSize];

	// offset for carousel items to prevent them overflowing screen
	$: itemWidthOffset = currentGap * ((currentItemToShow - 1) / currentItemToShow);

	let currentIndex = 0;
	function prevScroll() {
		// previous item index
		currentIndex = currentIndex <= 1 ? itemCount : currentIndex - 1;

		// applying visible item count to scroll items properly
		if (currentIndex !== itemCount) {
			currentIndex = currentIndex - currentItemToShow + 1;
		}

		// safety check if currentIndex goes below 1 somehow
		if (currentIndex <= 1) {
			currentIndex = 1;
		}

		const el = carouselContainer.querySelector(`[data-item-id="${currentIndex}"]`);
		el?.scrollIntoView({
			behavior: 'smooth'
		});
	}

	function nextScroll() {
		// next item index
		currentIndex = currentIndex >= itemCount ? 1 : currentIndex + 1;

		// applying visible item count to scroll items properly
		if (currentIndex !== 1) {
			currentIndex = currentIndex + currentItemToShow - 1;
		}

		// safety check if currentIndex goes over total item count somehow
		if (currentIndex >= itemCount) {
			currentIndex = itemCount;
		}

		// query target element and scroll to it
		const el = carouselContainer.querySelector(`[data-item-id="${currentIndex}"]`);
		el?.scrollIntoView({
			behavior: 'smooth'
		});
	}
</script>

<svelte:window bind:innerWidth={screenWidth} />

<div class="ksmsk-carousel-wrapper">
	{#if $$slots.prevArrow}
		<button on:click={prevScroll}>
			<slot name="prevArrow" />
		</button>
	{/if}
	<div
		bind:this={carouselContainer}
		style:gap="calc(0.25rem * {currentGap})"
		style:grid-template-columns="repeat({itemCount}, calc({100 / currentItemToShow}% - 0.25rem * {itemWidthOffset}))"
		class="ksmsk-carousel-container ksmsk-carousel-scrollbar"
	>
		{#each items as item, index}
			<div data-item-id={index + 1} class="ksmsk-carousel-item">
				<slot {item} />
			</div>
		{/each}
	</div>
	{#if $$slots.nextArrow}
		<button on:click={nextScroll}>
			<slot name="nextArrow" />
		</button>
	{/if}
</div>

<style lang="postcss" global>
	.ksmsk-carousel-wrapper {
		position: relative;
		overflow: hidden;
	}

	.ksmsk-carousel-wrapper button {
		border: none;
		background: none;
		padding: 0;
		margin: 0;
	}

	.ksmsk-carousel-container {
		display: grid;
		gap: 50px;
		overflow-x: auto;
		overflow-y: hidden;
		scroll-snap-type: x mandatory;
		grid-template-rows: repeat(1, minmax(0, 1fr));
	}

	.ksmsk-carousel-item {
		flex-shrink: 0;
		scroll-snap-align: start;
	}
	.ksmsk-carousel-item > * {
		width: 100%;
		height: 100%;
		object-fit: cover;
	}
	/* .ksmsk-carousel-scrollbar {
		scrollbar-color: rgba(142, 139, 139) transparent;
		scrollbar-width: 5px;
	} */
	.ksmsk-carousel-scrollbar::-webkit-scrollbar {
		display: none;
		/* width: 5px; */
	}
	/* .ksmsk-carousel-scrollbar::-webkit-scrollbar-track {
		background-color: transparent;
	}
	.ksmsk-carousel-scrollbar::-webkit-scrollbar-thumb {
		background-color: gray;
		border-radius: 10px;
	} */
</style>
