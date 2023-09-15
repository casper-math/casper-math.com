<script>
    import casper from 'casper-math'
    import katex from 'katex'

    let input = '2 * 3 + 4 * 5'
    let output = ''
    $: latex = katex.renderToString(input, { throwOnError: false })

    function go() {
        output = katex.renderToString(casper().go(input), { throwOnError: false })
    }
</script>

<div class="flex">
    <input
        bind:value={input}
        type="text"
        autofocus
        placeholder="Type an expression..."
        class="w-full px-6 py-4 text-lg border-2 border-r-0 border-gray-200 shadow-sm rounded-l-md focus:border-blue-600 focus:outline-none"
        class:font-mono={input !== ''}
    />

    <button
        on:click={go}
        class="flex items-center px-6 py-4 space-x-1 text-lg text-white transition bg-blue-600 group focus:outline-none focus:bg-blue-700 hover:bg-blue-700 rounded-r-md"
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

<div class="flex items-center p-6 my-4 bg-gray-200"><div class="w-20 font-bold">Input:</div> {@html latex}</div>
<div class="flex items-center p-6 my-4 bg-gray-200"><div class="w-20 font-bold">Output:</div> {@html output}</div>
