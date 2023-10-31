<script lang="ts">
    import type { Step } from 'casper-math/dist/interfaces'
    import katex from 'katex'

    export let step: Step
</script>

<div class="bg-white p-6 rounded-md shadow-md">
    <h3 class="text-blue-600 mb-2 font-bold">
        {step.name.charAt(0).toUpperCase() + step.name.slice(1)}
        {@html katex.renderToString(`\\left(${step.search} \\to ${step.replace}\\right)`)}
    </h3>

    <div>{@html katex.renderToString(step.result, { displayMode: true })}</div>
</div>
