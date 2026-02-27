<template>
    <div class="flex flex-col items-center justify-center py-24">
        <!-- Minimalist Dot Animation -->
        <div class="flex space-x-2 mb-6">
            <div class="w-2 h-2 bg-indigo-600 rounded-full animate-bounce [animation-delay:-0.3s]"></div>
            <div class="w-2 h-2 bg-indigo-600 rounded-full animate-bounce [animation-delay:-0.15s]"></div>
            <div class="w-2 h-2 bg-indigo-600 rounded-full animate-bounce"></div>
        </div>

        <div class="text-center">
            <h3 class="text-lg font-bold text-slate-900 uppercase tracking-wider">Waiting for emails</h3>
            <p class="text-sm text-indigo-600 mt-1 font-medium italic">Refreshing automatically...</p>
        </div>
    </div>
</template>

<script>
export default {
    name: 'WaitingMsg'
}
</script>
