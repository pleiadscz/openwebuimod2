<script lang="ts">
	import { WEBUI_BASE_URL } from '$lib/constants';
	import { safeImageUrl } from '$lib/utils/safeImageUrl';

	export let className = 'size-8';
	export let src = `${WEBUI_BASE_URL}/static/favicon.png`;
</script>

<div class="relative {className} shrink-0">
	<svg
		class="absolute inset-0 w-full h-full"
		xmlns="http://www.w3.org/2000/svg"
		viewBox="0 0 320 320"
	>
		<circle cx="160" cy="160" r="148" fill="#F0C020" />
	</svg>
	<img
		aria-hidden="true"
		src={safeImageUrl(src)}
		class="relative z-10 w-full h-full object-cover rounded-full p-[5%]"
		alt="profile"
		draggable="false"
		on:error={(e) => {
			e.currentTarget.src = src.includes('favicon.png') ? '/favicon.png' : src;
		}}
	/>
</div>
