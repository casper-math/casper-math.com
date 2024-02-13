<script lang="ts">
    import casper from 'casper-math'
    import type { Result } from 'casper-math/dist/interfaces'
    import katex from 'katex'
    import { onMount } from 'svelte'
    import { fly } from 'svelte/transition'
    import ShowStep from './ShowStep.svelte'

    let input: string = ''
    let preview: string = ''
    let result: Result = { result: '', steps: [] }

    $: try {
        let result = casper().options({ output: 'latex', actions: [] }).go(input)
        preview = katex.renderToString(result.result, { displayMode: true })
    } catch (error) {}

    function go() {
        try {
            result = casper().options({ output: 'latex' }).go(input)
        } catch (error) {}

        document.getElementById('form')?.scrollIntoView({ behavior: 'smooth' })
        window.history.pushState({}, '', `?input=${encodeURIComponent(input)}`)
    }

    function showExample(text: string) {
        input = text
        go()
    }

    onMount(() => {
        const urlParams = new URLSearchParams(window.location.search)
        const query = urlParams.get('input')
        if (query) input = query
    })
</script>

<form
    on:submit|preventDefault={go}
    id="form"
    class="container absolute flex items-center w-full -translate-y-1/2 lg:mx-0"
>
    <!-- svelte-ignore a11y-autofocus -->
    <input
        bind:value={input}
        on:input={() => (result.result = '')}
        type="text"
        autofocus
        placeholder="Typ een expressie..."
        class="w-full px-8 py-6 text-lg border border-gray-200 shadow-lg md:text-base md:px-6 sm:px-4 md:py-4 sm:py-3 focus:outline-none rounded-l-md"
        class:font-mono={input !== ''}
        class:tracking-wide={input === ''}
    />

    <button
        type="submit"
        class="flex items-center p-6 space-x-1 text-lg transition bg-gray-200 border border-gray-200 shadow-lg md:text-base md:px-6 sm:px-4 md:py-4 sm:py-3 hover:bg-gray-300 hover:border-gray-300 focus:border-gray-300 focus:outline-none focus:bg-gray-300 focus:shadow-lg rounded-r-md group"
    >
        <span>Gaan!</span>

        <svg
            xmlns="http://www.w3.org/2000/svg"
            fill="none"
            viewBox="0 0 24 24"
            stroke-width="1.5"
            stroke="currentColor"
            class="w-6 h-6 transition duration-300 group-focus:translate-x-2 group-hover:translate-x-2"
        >
            <path stroke-linecap="round" stroke-linejoin="round" d="M4.5 12h15m0 0l-6.75-6.75M19.5 12l-6.75 6.75" />
        </svg>
    </button>
</form>

<div class="pt-16 mb-24 space-y-24 md:mb-16 sm:mb-8 md:space-y-16 sm:space-y-8 bg-gradient-to-b from-gray-200 to-white">
    <div>
        {#if preview !== '' && input !== ''}
            <div class="container flex items-center mb-10 space-x-3 text-lg md:text-base">
                <p><strong>Jouw tekst:</strong></p>
                <div>{@html preview}</div>
            </div>
        {/if}

        {#if result.result !== ''}
            <div class="container">
                <div
                    transition:fly={{ y: 50, duration: 500 }}
                    class="w-full p-6 text-left bg-white rounded-md shadow-md"
                >
                    <h3 class="mb-2 font-bold text-blue-600 capitalize">Resultaat</h3>

                    <div>{@html katex.renderToString(result.result, { displayMode: true })}</div>
                </div>
            </div>

            {#if result.steps.length > 0}
                <div class="container">
                    <h2
                        transition:fly|global={{ y: 50, duration: 500, delay: 100 }}
                        class="mt-10 mb-6 text-lg font-bold">Stappen ({result.steps.length})</h2
                    >
                </div>
            {/if}

            <ol class="container space-y-4">
                {#each result.steps as step, key}
                    <li
                        in:fly|global={{ y: 50, delay: key * 100 + 200, duration: 500 }}
                        out:fly|global={{ y: 50, duration: 300 }}
                    >
                        <ShowStep {step} />
                    </li>
                {/each}
            </ol>
        {/if}
    </div>

    <div class="container mt-12">
        <h2 class="mb-10 text-4xl font-bold text-center md:text-3xl md:text-left md:mb-8 sm:mb-6 sm:text-2xl">
            Probeer een voorbeeld
        </h2>

        <ul class="grid grid-cols-3 gap-6 md:grid-cols-2 sm:grid-cols-1 md:gap-4">
            {#each ['3^2 + 4 * 5 - 1', '3x + 6x - 4x', '6x^3 + 2x * 3x^2', '2x * (x + 3)', '(x + y)^3', '(2x + 1)(3x + 4)'] as example}
                <li>
                    <button
                        on:click={() => showExample(example)}
                        class="flex items-center justify-between w-full px-10 py-6 transition duration-100 bg-white border border-gray-300 rounded-md shadow-md md:px-8 md:py-4 md:text-base hover:scale-105 group hover:shadow-lg"
                    >
                        <span class="md:hidden" />
                        <span>
                            {@html katex.renderToString(
                                casper().options({ actions: [], output: 'latex' }).go(example).result
                            )}
                        </span>
                        <svg
                            xmlns="http://www.w3.org/2000/svg"
                            fill="none"
                            viewBox="0 0 24 24"
                            stroke-width="1.5"
                            stroke="currentColor"
                            class="w-6 h-6 text-gray-500"
                        >
                            <path
                                stroke-linecap="round"
                                stroke-linejoin="round"
                                d="M13.5 4.5L21 12m0 0l-7.5 7.5M21 12H3"
                            />
                        </svg>
                    </button>
                </li>
            {/each}
        </ul>
    </div>
</div>
