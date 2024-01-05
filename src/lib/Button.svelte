<script lang="ts">
	export let size: string = 'small';
	export let shadow: boolean = false;
	export let bgColor = 'inherit';
	export let textColor = 'inherit';
</script>

<button
	class:size-sm={size === 'small'}
	class:size-lg={size === 'large'}
	class:shadow
	style:--buttonBgColor={bgColor}
	style:--buttonTextColor={textColor}
	{...$$restProps}
>
	{#if $$slots.leftContent}
		<div class="left-content">
			<slot name="leftContent" />
		</div>
	{/if}
	<slot>Fallback</slot>
</button>

<style lang="scss">
	button {
		display: flex;
		align-items: center;
		// border: none;
		// cursor: pointer;
		background-color: var(--buttonBgColor);
		color: var(--buttonTextColor);
		padding: 15px 20px;
		font-weight: bold;
		border-radius: 5px;
		.left-content {
			margin-right: 10px;
		}
		&.size-sm {
			padding: 15px 20px;
		}
		&.size-lg {
			padding: 20px 25px;
			font-size: 20px;
		}
		&.shadow {
			box-shadow: 0 0 10px rgba(1, 1, 1, 0.3);
		}
		&:hover {
			background-image: linear-gradient(rgba(0, 0, 0, 0.4) 0 0);
		}
		&:active {
			background-image: linear-gradient(rgba(255, 255, 255, 0.1) 0 0);
		}
		&:disabled {
			cursor: not-allowed;
			background-image: linear-gradient(rgba(0, 0, 0, 0.4) 0 0);
		}
	}
</style>
