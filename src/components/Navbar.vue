<template>
    <nav class="w-full bg-slate-900 border-b border-slate-800">
        <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8">
            <div class="flex items-center justify-between h-20">
                <div class="flex-shrink-0 flex items-center">
                    <router-link to="/" class="flex items-center gap-2">
                        <img src="/errehub-dark.webp" width="120" height="40" alt="Errehub UI logo"
                            class="h-10 w-auto" />
                    </router-link>
                </div>

                <!-- Desktop Navigation -->
                <div class="hidden lg:flex flex-1 items-center justify-center px-8">
                    <ul class="flex items-center space-x-6">
                        <li v-for="link in navigation" :key="link.id">
                            <router-link :to="link.router"
                                class="text-yellow-300 hover:text-indigo-400 Transition-all font-medium">
                                {{ link.title }}
                            </router-link>
                        </li>
                    </ul>
                </div>

                <!-- Right Side Actions -->
                <div class="hidden lg:flex items-center">
                    <router-link to="/temp-number"
                        class="bg-yellow-400 hover:bg-yellow-500 text-slate-900 px-6 py-2.5 rounded-md font-bold transition-all shadow-lg active:scale-95">
                        Temp Number
                    </router-link>
                </div>

                <!-- Mobile menu button -->
                <div class="lg:hidden">
                    <button
                        class="inline-flex items-center justify-center p-2 rounded-lg text-yellow-300 hover:bg-slate-800 focus:outline-none transition-colors"
                        @click="menuOpen()">
                        <span class="sr-only">Open main menu</span>
                        <IconMenu v-if="!open" class="block h-6 w-6" />
                        <IconClose v-else class="block h-6 w-6" />
                    </button>
                </div>
            </div>
        </div>

        <!-- Mobile Navigation -->
        <div v-show="open" class="lg:hidden bg-slate-900 border-t border-slate-800">
            <div class="px-2 pt-2 pb-3 space-y-1 sm:px-3">
                <router-link v-for="link in navigation" :key="link.id" :to="link.router"
                    class="block px-3 py-4 rounded-lg text-base font-medium text-yellow-300 hover:bg-slate-800 transition-colors">
                    {{ link.title }}
                </router-link>
                <router-link to="/temp-number"
                    class="block px-3 py-4 rounded-lg text-base font-medium bg-yellow-400 text-slate-900 hover:bg-yellow-500 transition-colors mt-4 text-center">
                    Temp Number
                </router-link>
            </div>
        </div>
    </nav>
</template>

<script>
import { ref } from 'vue';
import IconMenu from './icons/IconMenu.vue';
import IconClose from './icons/IconClose.vue';

export default {
    components: {
        IconMenu,
        IconClose
    },
    data() {
        return {
            navigation: [
                { id: 1, title: "Home", router: "/" },
            ]
        }
    },
    setup() {
        const open = ref(false);
        const menuOpen = () => {
            open.value = !open.value;
        }
        return { open, menuOpen };
    }
}
</script>
