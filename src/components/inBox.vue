<template>
    <div class="w-full max-w-5xl mx-auto my-16">
        <div class="rounded-[2.5rem] overflow-hidden border border-slate-100 bg-white shadow-2xl shadow-slate-200/50">
            <!-- Header -->
            <div class="flex items-center justify-between w-full bg-slate-50/50 py-7 px-10 border-b border-slate-100">
                <div class="w-1/3 text-[10px] font-black text-slate-400 uppercase tracking-[0.3em]">sender</div>
                <div class="w-1/3 text-[10px] font-black text-slate-400 uppercase tracking-[0.3em] text-center">subject
                </div>
                <div class="w-1/3 text-[10px] font-black text-slate-400 uppercase tracking-[0.3em] text-right">inbox
                    status</div>
            </div>

            <!-- Content Area -->
            <div class="min-h-[500px] p-8 sm:p-12">
                <slot></slot>
                <div v-if="isWait">
                    <WaitingMsg />
                </div>
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
            type: Boolean,
            required: true,
        }
    }
}
</script>
