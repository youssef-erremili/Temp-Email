<template>
    <HeroSection />
    <router-view />
</template>

<script>

import HeroSection from './components/HeroSection.vue';
export default {
    components: {
        HeroSection
    }
}
</script>