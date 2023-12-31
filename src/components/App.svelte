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
        placeholder="Type an expression..."
        class="w-full px-8 py-6 text-lg border border-gray-200 shadow-lg md:text-base md:px-6 sm:px-4 md:py-4 sm:py-3 focus:outline-none rounded-l-md"
        class:font-mono={input !== ''}
        class:tracking-wide={input === ''}
    />

    <button
        type="button"
        class="absolute right-[calc(108px+8*4px+6*4px)] md:right-[calc(88px+6*4px+6*4px)] sm:right-[calc(88px+4*4px+6*4px)] text-gray-600 hover:text-gray-900 hover:rotate-45 transition duration-300"
    >
        <svg
            xmlns="http://www.w3.org/2000/svg"
            fill="none"
            viewBox="0 0 24 24"
            stroke-width="1.5"
            stroke="currentColor"
            class="w-6 h-6"
        >
            <path
                stroke-linecap="round"
                stroke-linejoin="round"
                d="M9.594 3.94c.09-.542.56-.94 1.11-.94h2.593c.55 0 1.02.398 1.11.94l.213 1.281c.063.374.313.686.645.87.074.04.147.083.22.127.324.196.72.257 1.075.124l1.217-.456a1.125 1.125 0 011.37.49l1.296 2.247a1.125 1.125 0 01-.26 1.431l-1.003.827c-.293.24-.438.613-.431.992a6.759 6.759 0 010 .255c-.007.378.138.75.43.99l1.005.828c.424.35.534.954.26 1.43l-1.298 2.247a1.125 1.125 0 01-1.369.491l-1.217-.456c-.355-.133-.75-.072-1.076.124a6.57 6.57 0 01-.22.128c-.331.183-.581.495-.644.869l-.213 1.28c-.09.543-.56.941-1.11.941h-2.594c-.55 0-1.02-.398-1.11-.94l-.213-1.281c-.062-.374-.312-.686-.644-.87a6.52 6.52 0 01-.22-.127c-.325-.196-.72-.257-1.076-.124l-1.217.456a1.125 1.125 0 01-1.369-.49l-1.297-2.247a1.125 1.125 0 01.26-1.431l1.004-.827c.292-.24.437-.613.43-.992a6.932 6.932 0 010-.255c.007-.378-.138-.75-.43-.99l-1.004-.828a1.125 1.125 0 01-.26-1.43l1.297-2.247a1.125 1.125 0 011.37-.491l1.216.456c.356.133.751.072 1.076-.124.072-.044.146-.087.22-.128.332-.183.582-.495.644-.869l.214-1.281z"
            />
            <path stroke-linecap="round" stroke-linejoin="round" d="M15 12a3 3 0 11-6 0 3 3 0 016 0z" />
        </svg>
    </button>

    <button
        type="submit"
        class="flex items-center p-6 space-x-1 text-lg transition bg-gray-200 border border-gray-200 shadow-lg md:text-base md:px-6 sm:px-4 md:py-4 sm:py-3 hover:bg-gray-300 hover:border-gray-300 focus:border-gray-300 focus:outline-none focus:bg-gray-300 focus:shadow-lg rounded-r-md group"
    >
        <span>Go!</span>

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
                <p><strong>Your Input:</strong></p>
                <div>{@html preview}</div>
            </div>
        {/if}

        {#if result.result !== ''}
            <div class="container">
                <div
                    transition:fly={{ y: 50, duration: 500 }}
                    class="w-full p-6 text-left bg-white rounded-md shadow-md"
                >
                    <h3 class="mb-2 font-bold text-blue-600 capitalize"> Result </h3>

                    <div>{@html katex.renderToString(result.result, { displayMode: true })}</div>
                </div>
            </div>

            {#if result.steps.length > 0}
                <div class="container">
                    <h2
                        transition:fly|global={{ y: 50, duration: 500, delay: 100 }}
                        class="mt-10 mb-6 text-lg font-bold">Steps ({result.steps.length})</h2
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
            Try an example
        </h2>

        <ul class="grid grid-cols-3 gap-6 md:grid-cols-2 sm:grid-cols-1 md:gap-4">
            {#each ['3^2 + 4 * 5 - 1', '3x + 6x - 4x', '6x^3 + 2x * 3x^2'] as example}
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

    <div class="container">
        <div
            class="grid grid-cols-2 gap-12 p-6 text-white rounded-md shadow-xl md:grid-cols-1 md:gap-6 bg-gradient-to-tr from-blue-600 to-blue-500"
        >
            <div class="col-span-1 p-6 pr-0 md:p-0">
                <h3 class="mb-4 text-3xl font-bold md:text-2xl">Use Casper in your project</h3>
                <p class="mb-6">Capser is an NPM package that you can use and configure to meet your needs.</p>
                <a
                    href="https://www.npmjs.com/package/casper-math"
                    class="inline-block px-6 py-3 text-blue-600 transition bg-white rounded-md shadow-md md:px-4 md:py-2 hover:shadow-xl hover:scale-110"
                >
                    Check it out
                </a>
            </div>
            <div class="col-span-1 p-6 overflow-x-auto text-lg bg-blue-900 rounded-md shadow-md md:p-4 md:text-base">
                <pre
                    ><span class="text-red-300">import</span> casper <span class="text-red-300">from</span> <span
                        class="text-blue-200">'casper-math'</span
                    ><br /><br /><span class="text-fuchsia-300">casper</span>()<br />    .<span
                        class="span text-fuchsia-300">options</span
                    >(&lbrace; output: '<span class="text-blue-200">string</span>'  &rbrace;)<br />    .<span
                        class="text-fuchsia-300">go</span
                    >(<span class="text-blue-200">'2*x + 4*x'</span>)<br />    .<span class="span text-fuchsia-300"
                        >result</span
                    > <span class="text-slate-400">// 6 * x</span></pre
                >
            </div>
        </div>
    </div>
</div>
