<script>
	import { createEventDispatcher } from 'svelte';
	import { removeLastWordFromString } from '$lib/utils';
	import { getPrompts } from '$lib/apis/prompts';
	import { getKnowledgeBases } from '$lib/apis/knowledge';

	import Prompts from './Commands/Prompts.svelte';
	import Knowledge from './Commands/Knowledge.svelte';
	import Models from './Commands/Models.svelte';
	import Skills from './Commands/Skills.svelte';
	import Emojis from './Commands/Emojis.svelte';

	const dispatch = createEventDispatcher();

	export let prompt = '';
	export let files = [];

	let commandElement = null;

	// Expose selectUp/selectDown so parent can call them
	export const selectUp = () => {
		commandElement?.selectUp();
	};
	export const selectDown = () => {
		commandElement?.selectDown();
	};
	export const select = () => {
		commandElement?.select();
	};

	// Derive the current command token from the last word of the last line
	let command = '';
	$: command = prompt?.split('\n').pop()?.split(' ')?.pop() ?? '';

	// Derive the query (everything after the trigger character)
	let char = '';
	let query = '';
	$: {
		if (['/', '#', '@', '$', ':'].includes(command?.charAt(0))) {
			char = command.charAt(0);
			query = command.slice(1);
		} else if (command?.slice(0, 2) === '\\#') {
			char = '#';
			query = command.slice(2);
		} else {
			char = '';
			query = '';
		}
	}

	$: show = char !== '';
</script>

{#if show}
	<div
		id="commands-container"
		class="px-2 mb-2 text-left w-full absolute bottom-0 left-0 right-0 z-10"
	>
		<div class="flex w-full rounded-xl border border-gray-100 dark:border-gray-850">
			<div
				class="max-h-60 flex flex-col w-full rounded-xl bg-white dark:bg-gray-900 dark:text-gray-100 overflow-y-auto scrollbar-thin p-1"
			>
				{#if char === '/'}
					<Prompts
						bind:this={commandElement}
						{query}
						onSelect={(e) => {
							const { type, data } = e;
							if (type === 'prompt') {
								prompt = removeLastWordFromString(prompt, command) + data.content;
							}
						}}
					/>
				{:else if char === '#'}
					<Knowledge
						bind:this={commandElement}
						{query}
						onSelect={(e) => {
							const { type, data } = e;
							prompt = removeLastWordFromString(prompt, command);
							if (type === 'knowledge') {
								if (!files.find((f) => f.id === data.id)) {
									files = [...files, { ...data, status: 'processed' }];
								}
								dispatch('upload', { type: 'file', data });
							} else if (type === 'web') {
								dispatch('upload', { type: 'web', data });
							}
						}}
					/>
				{:else if char === '@'}
					<Models
						bind:this={commandElement}
						{query}
						onSelect={(e) => {
							const { type, data } = e;
							if (type === 'model') {
								prompt = removeLastWordFromString(prompt, command);
								dispatch('select', { type: 'model', data });
							}
						}}
					/>
				{:else if char === '$'}
					<Skills
						bind:this={commandElement}
						{query}
						onSelect={(e) => {
							const { type, data } = e;
							if (type === 'skill') {
								prompt = removeLastWordFromString(prompt, command);
								dispatch('select', { type: 'skill', data });
							}
						}}
					/>
				{:else if char === ':'}
					<Emojis
						bind:this={commandElement}
						{query}
						onSelect={(e) => {
							const { type, data } = e;
							if (type === 'emoji') {
								prompt = removeLastWordFromString(prompt, command) + data.emoji;
							}
						}}
					/>
				{/if}
			</div>
		</div>
	</div>
{/if}
