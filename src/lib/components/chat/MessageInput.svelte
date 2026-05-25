<script lang="ts">
  import Plus from '../icons/Plus.svelte';
  import ArrowUp from '../icons/ArrowUp.svelte';
  import Square from '../icons/Square.svelte';

  interface Props {
    prompt: string;
    onSubmit: () => void;
    stopResponse?: () => void;
    generating?: boolean;
    placeholder?: string;
    onChange?: (val: any) => void;
  }

  let {
    prompt = $bindable(''),
    onSubmit,
    stopResponse,
    generating = false,
    placeholder = 'Zapytaj o cokolwiek',
    onChange
  }: Props = $props();

  // Compatibility with existing codebase
  export const setText = (text: string) => {
    prompt = text;
  };

  function handleKeyDown(e: KeyboardEvent) {
    if (e.key === 'Enter' && !e.shiftKey) {
      e.preventDefault();
      if (prompt.trim() && !generating) onSubmit();
    }
  }

  function handleSubmit(e: Event) {
    e.preventDefault();
    if (generating) {
      stopResponse?.();
    } else if (prompt.trim()) {
      onSubmit();
    }
  }

  $effect(() => {
    if (onChange) {
      onChange({ prompt });
    }
  });
</script>

<form onsubmit={handleSubmit} class="w-full">
  <div class="flex items-center gap-2 rounded-full border border-neutral-200 dark:border-neutral-800 bg-white dark:bg-neutral-900 px-3 py-2 shadow-[0_0_40px_rgba(0,0,0,0.04)] dark:shadow-none">
    <button
      type="button"
      aria-label="Dodaj"
      class="flex h-9 w-9 shrink-0 items-center justify-center rounded-full text-neutral-700 dark:text-neutral-200 hover:bg-neutral-100 dark:hover:bg-neutral-800 transition-colors"
    >
      <Plus className="h-5 w-5" strokeWidth="2" />
    </button>

    <input
      type="text"
      bind:value={prompt}
      onkeydown={handleKeyDown}
      {placeholder}
      disabled={generating}
      class="flex-1 bg-transparent px-1 py-2 text-[15px] text-neutral-900 dark:text-neutral-100 placeholder:text-neutral-500 dark:placeholder:text-neutral-400 focus:outline-none disabled:opacity-60"
    />

    <button
      type="submit"
      aria-label={generating ? 'Zatrzymaj' : 'Wyślij'}
      class="flex h-9 w-9 shrink-0 items-center justify-center rounded-full bg-black dark:bg-white text-white dark:text-black hover:bg-neutral-800 dark:hover:bg-neutral-200 transition-colors"
    >
      {#if generating}
        <Square className="h-4 w-4" fill="currentColor" strokeWidth="0" />
      {:else}
        <ArrowUp className="h-5 w-5" strokeWidth="2.5" />
      {/if}
    </button>
  </div>
</form>
