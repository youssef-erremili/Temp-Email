<template>
    <div class="w-fit mx-auto text-center mt-20">
        <IconWait class="w-fit inline-table" />
        <p class="text-[20px]">Your inbox is empty</p>
        <p>Waiting for incoming emails</p>
    </div>
</template>

<script>
import IconWait from './icons/IconWait.vue'

export default {
    components: {
        IconWait
    }
}
</script>
