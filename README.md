# Svelte Carousel

Super lightweight carousel component for svelte. The component uses new CSS snap feature. Native scrolling feel in mobile!

## Installation

```bash
npm install -S  @ksmsk/svelte-carousel
```

## Usage

```jsx
<script>
  import SvelteCarousel from '@ksmsk/svelte-carousel';

  let items = [...]
</script>

<SvelteCarousel {items} let:item>
  <-- Arrow Elements and Styling -->
  <div slot="prevArrow">back</div>
  <div slot="prevArrow">forward</div>

  <-- Default slot from iterated items -->
  <div>{item}</div>
</SvelteCarousel>
```

## Props

| prop        | default                                            | type               | description                                                       |
|-------------|----------------------------------------------------|--------------------|-------------------------------------------------------------------|
| items       | `[]`                                               | `array`            | items to show in carousel                                         |
| breakpoints | `{sm: 640, md: 768, lg: 1024, xl: 1280, xxl:1440}` | `object`           | breakpoints for various screen sizes                              |
| gap         | `8`                                                | `number | object`  | space between items numeric value converts to rem (0.25rem * gap) |
| itemToShow  | `1`                                                | `number | object`  | item count to be shown on screen                                  |
