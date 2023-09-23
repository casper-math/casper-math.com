<script lang="ts">
    import casper from 'casper-math'
    import katex from 'katex'

    let input: string = '(2 + 3) / (4 * 5)'
    let preview: string = ''
    let output: string = ''

    $: try {
        let result = casper().options({ output: 'latex', actions: [] }).go(input)
        preview = katex.renderToString(result.result, { displayMode: true })
    } catch (error) {}

    function go() {
        try {
            let result = casper().options({ output: 'latex' }).go(input)
            output = katex.renderToString(result.result, { displayMode: true })
        } catch (error) {}
    }

    function show(text: string) {
        input = text
        go()
    }
</script>

<div class="container mx-[calc((100vw-1024px)/2)] w-full flex items-center absolute -translate-y-1/2">
    <!-- svelte-ignore a11y-autofocus -->
    <input
        bind:value={input}
        on:keydown={() => (output = '')}
        type="text"
        autofocus
        placeholder="Type an expression..."
        class="w-full px-8 py-6 text-lg border border-gray-200 shadow-lg focus:outline-none rounded-l-md"
        class:font-mono={input !== ''}
        class:tracking-wide={input === ''}
    />

    <button class="absolute right-[140px] text-gray-600 hover:text-gray-900 hover:rotate-45 transition duration-300">
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
        on:click={go}
        class="flex items-center p-6 space-x-1 text-lg transition bg-gray-200 border border-gray-200 shadow-lg hover:bg-gray-300 hover:border-gray-300 focus:border-gray-300 focus:outline-none focus:bg-gray-300 focus:shadow-lg rounded-r-md group"
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
</div>

<div class="pt-16 mb-24 space-y-24 bg-gradient-to-b from-gray-200 to-white">
    <div>
        {#if preview !== '' && input !== ''}
            <div class="container flex items-center space-x-3 text-lg">
                <p><strong>Your Input:</strong></p>
                <div>{@html preview}</div>
            </div>
        {/if}

        {#if output !== ''}
            <div class="container flex items-center space-x-3 text-lg">
                <p><strong>Output:</strong></p>
                <div>{@html output}</div>
            </div>
        {/if}
    </div>

    <div class="container mt-12">
        <h2 class="mb-10 text-4xl font-bold text-center">Try an example</h2>
        <ul class="grid grid-cols-3 gap-6">
            {#each ['2 * x', '3 + 3', '2 * x + 4 * x', '3 + 3', '2 * x + 4 * x', 'sin(2 * 3 * pi)'] as example}
                <li>
                    <button
                        on:click={() => show(example)}
                        class="flex items-center justify-between w-full px-10 py-6 text-lg transition duration-100 bg-white border border-gray-300 rounded-md shadow-md hover:scale-105 group hover:shadow-lg"
                    >
                        <span />
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
            class="grid grid-cols-2 gap-12 p-6 text-white rounded-md shadow-xl bg-gradient-to-tr from-blue-600 to-blue-500"
        >
            <div class="col-span-1 p-6">
                <h3 class="mb-4 text-3xl font-bold">Use Casper in your project</h3>
                <p class="mb-6">Capser is an NPM package that you can use and configure to meet your needs.</p>
                <a
                    href="https://www.npmjs.com/package/casper-math"
                    class="inline-block px-6 py-3 text-blue-600 transition bg-white rounded-md shadow-md hover:shadow-xl hover:scale-110"
                >
                    Check it out
                </a>
            </div>
            <div class="col-span-1 p-6 text-lg bg-blue-900 rounded-md shadow-md">
                <pre
                    ><span class="text-red-300">import</span> casper <span class="text-red-300">from</span> <span
                        class="text-blue-200">'casper-math'</span
                    ><br /><br /><span class="text-fuchsia-300">casper</span>()<br />    .<span
                        class="span text-fuchsia-300">options</span
                    >(&lbrace; output: '<span class="text-blue-200">string</span>'  &rbrace;)<br />    .<span
                        class="text-fuchsia-300">go</span
                    >(<span class="text-blue-200">'2*x + 4*x'</span>) <span class="text-slate-400">// 6 * x</span>
                </pre>
            </div>
        </div>
    </div>
</div>
