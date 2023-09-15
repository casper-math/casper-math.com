<script>
import casper from 'casper-math'

let input = '2 + 3 + 7'

$: output = casper().go(input)
</script>

<input
	bind:value={input}
	type="text"
	autofocus
	placeholder="Type an expression..."
	class="w-full px-6 py-4 text-lg border border-gray-300 rounded-md shadow-sm focus:ring-1 focus:ring-blue-600 focus:border-blue-600 focus:outline-none"
	class:font-mono={input !== ''}
/>

<div>{output}</div>
