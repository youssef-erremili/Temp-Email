<template>
    <nav class="w-full py-5 bg-slate-900">
        <div class="items-center px-4 max-w-screen-xl mx-auto lg:flex lg:px-8">
            <div class="flex items-center justify-between py-3 backdrop-filter lg:py-4 lg:block">
                <router-link to="/" class="backdrop-filter">
                    <img src="/errehub-dark.webp" width="120" class="backdrop-filter" height="50"
                        alt="Errehub UI logo" />
                </router-link>
                <div class="lg:hidden">
                    <button class="text-gray-700 outline-none p-2 rounded-md focus:border-gray-400 focus:border"
                        @click="menuOpen()">
                        <IconClose :class="[open ? 'block' : 'hidden']" />
                        <IconMenu :class="[open ? 'hidden' : 'block']" />
                    </button>
                </div>
            </div>
            <div class="flex-1 justify-between flex-row-reverse lg:overflow-visible lg:flex lg:pb-0 lg:pr-0 lg:h-auto"
                :class="[open ? 'h-screen pb-20 overflow-auto pr-4' : 'hidden']">
                <div>
                    <ul class="flex flex-col-reverse space-x-0 lg:space-x-6 lg:flex-row">
                        <li class="mt-8 lg:mt-0">
                            <router-link
                                class="py-3 px-4 text-center font-medium text-black bg-yellow-400 hover:bg-indigo-700 rounded-md shadow block lg:inline"
                                to="temp-number">
                                Temp Number
                            </router-link>
                        </li>
                    </ul>
                </div>
                <div class="flex-1">
                    <ul class="justify-center items-center space-y-8 lg:flex lg:space-x-6 lg:space-y-0">
                        <li v-for="link in navigation" :key="link.id" class="text-yellow-300 hover:text-indigo-600">
                            <router-link :to="link.router">{{ link.title }}</router-link>
                        </li>
                    </ul>
                </div>
            </div>
        </div>
    </nav>
</template>

<style scoped>
.sticky-nav {
    animation: sticky-nav 300ms;
}

@keyframes sticky-nav {
    0% {
        transform: translateY(-6em)
    }

    100% {
        transform: translateY(0)
    }
}
</style>

<script>
import { ref } from 'vue';
import IconMenu from './icons/IconMenu.vue';
import IconClose from './icons/IconClose.vue';
export default {
    components: {
        IconMenu,
        IconClose
    },
    data: function () {
        return {
            navigation: [
                { title: "Home", router: "/" },
                { title: "Blog", router: "/" },
                { title: "Contact", router: "/" },
                { title: "About", router: "/" },
            ]
        }
    },
    setup() {
        let open = ref(false);
        function menuOpen() {
            open.value = !open.value;
        }
        return { open, menuOpen };
    },

}
</script>