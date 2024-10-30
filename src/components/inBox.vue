<template>
    <div>
        <div class="relative w-3/4 my-10 h-96 mx-auto rounded-md overflow-hidden bg-slate-50 border">
            <div class="flex items-center justify-between w-full bg-slate-800 py-4 px-8">
                <section class="text-lg capitalize">
                    <h2>sender</h2>
                </section>
                <section class="text-lg capitalize">
                    <h2>subject</h2>
                </section>
                <section class="text-lg capitalize">
                    <h2>view</h2>
                </section>
            </div>
            <div class="text-slate-900">
                <slot></slot>
                <WaitingMsg  v-if="isWait" />
            </div>
        </div>
    </div>
</template>

<script>
import WaitingMsg from './WaitingMsg.vue';

export default {
    components: {
        WaitingMsg
    },
    props: {
        isWait: {
            Type: String,
            required: true,
        }
    }
}
</script>
