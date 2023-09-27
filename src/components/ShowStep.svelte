<script lang="ts">
    import type { Step } from 'casper-math/dist/interfaces'
    import katex from 'katex'
    import { fade } from 'svelte/transition'

    export let step: Step
    let expanded: boolean = false
</script>

<button class="w-full text-left bg-white p-6 rounded-md shadow-md" on:click={() => (expanded = !expanded)}>
    <div class="flex items-center justify-between">
        <h3 class="text-blue-600 mb-2 font-bold capitalize">
            {step.name}
        </h3>

        <svg
            xmlns="http://www.w3.org/2000/svg"
            fill="none"
            viewBox="0 0 24 24"
            stroke-width="1.5"
            stroke="currentColor"
            class="w-6 h-6 text-gray-500 hover:text-black duration-300 transition {expanded ? 'rotate-180' : ''}"
        >
            <path
                stroke-linecap="round"
                stroke-linejoin="round"
                d="M15 11.25l-3-3m0 0l-3 3m3-3v7.5M21 12a9 9 0 11-18 0 9 9 0 0118 0z"
            />
        </svg>
    </div>

    {#if expanded}
        <div in:fade class="flex mb-3 items-center space-x-1">
            <span>Replace</span>
            <span>{@html katex.renderToString(step.search, { displayMode: true })}</span>
            <span>by</span>
            <span>{@html katex.renderToString(step.replace, { displayMode: true })}</span>
            <span>.</span>
        </div>
    {/if}

    <div>{@html katex.renderToString(step.result, { displayMode: true })}</div>
</button>
