<template>
    <div>
        <div v-if="isLoading" class="h-1 w-full fixed top-0 z-20">
            <ProgressBar mode="indeterminate" style="height:4px; background:#0f172a;"></ProgressBar>
        </div>
        <Navbar />
        <div class="text-center h-auto bg-slate-900 pt-10 pb-16 md:px-8">
            <div class="space-y-4 flex-1">
                <h1 class="text-white capitalize font-bold text-4xl xl:text-5xl">
                    free temporary email generator
                    <span class="text-indigo-600">TempMail</span>
                </h1>
                <p class="text-slate-400 text-balance text-sm text-center leading-relaxed sm:mx-auto">
                    Forget about spam, advertising mailings, hacking and attacking robots. Keep your real mailbox clean
                    and secure. Temp Mail provides temporary, secure, anonymous, free, disposable email address.
                </p>
                <div>
                    <form @submit.prevent="generateFakeDomains()">
                        <div class="py-10 w-fit m-auto rounded-md border-2 px-20 mt-10 border-slate-600 border-dashed">
                            <h2 class="text-lg font-medium text-slate-100 my-4">Your Temporary Email Address :</h2>
                            <div class="flex justify-center items-center">
                                <input v-model="tempMail" type="text" placeholder="tempMail address" class="pointer-events-none select-none bg-slate-800 rounded-full py-4 px-7 w-[30rem] text-lg text-white font-sans border-none outline-none" name="tempmail" readonly>
                                <button @click="copyTempMail(tempMail)" type="button" class="cursor-pointer rounded-full h-auto bg-indigo-600 ml-2 p-4">
                                    <IconCopy color="#fff"/>
                                </button>
                            </div>
                        </div>
                    </form>
                    <div class="flex justify-evenly w-[50rem] mx-auto mt-12">
                        <button :disabled="isLoading" class="flex items-center capitalize text-[17px] font-[400] rounded-3xl h-[43px] px-10 bg-indigo-600 text-white"
                            @click="generateFakeDomains()">
                            <IconGenerate class="w-[22.5px] mt-[2px] mr-2" />
                            generate
                        </button>
                        <button :disabled="isLoading" class="flex items-center capitalize text-[17px] font-[400] rounded-3xl h-[43px] px-10 bg-indigo-600 text-white"
                            @click="copyTempMail(tempMail)">
                            <IconCopy class="w-[22.5px] mr-2" />
                            copy
                        </button>
                        <button :disabled="isLoading" class="flex items-center capitalize text-[17px] font-[400] rounded-3xl h-[43px]  px-10 bg-indigo-600 text-white"
                            @click="refreshPage()">
                            <IocnRefresh class="w-[22.5px] mr-2" />
                            refresh
                        </button>
                        <button :disabled="isLoading" class="flex items-center capitalize text-[17px] font-[400] rounded-3xl h-[43px]  px-10 bg-indigo-600 text-white">
                            <IconDelete class="w-[22.5px] mr-2" />
                            delete
                        </button>
                    </div>
                </div>
            </div>
        </div>
        <inBox :isWait="isWait">
            <RecievedMsg v-if="isMsg">
                <div v-for="(msg, index) in messages" :key="index" :class="index === 0 ? '' : 'mt-3'" class="flex items-center justify-between bg-white py-2 px-4">
                    <div class="w-1/3">
                        <h1 class="font-medium text-slate-900 uppercase">{{ msg.fullname }}</h1>
                        <p class="text-sm text-slate-500">{{ msg.address }}</p>
                    </div>
                    <div class="w-1/3 text-center pl-[1.3rem] capitalize">
                        <h3>{{ msg.subject }}</h3>
                    </div>
                    <div class="w-1/3 text-right">
                        <button type="button" @click="getMessage(msg.id)" class="bg-indigo-500 rounded-md text-base capitalize text-white py-1 px-4">view</button>
                    </div>
                </div>
            </RecievedMsg>
        </inBox>
    </div>
    <div class="flex justify-center">
        <Dialog v-model:visible="visible" maximizable modal header="Inbox" :style="{ width: '65rem' }"
            :breakpoints="{ '1199px': '75vw', '575px': '90vw' }">
            <div class="m-0 text-slate-400 flex justify-between px-3 py-4 text-[15px] border-b-2 border-slate-700">
                <section>
                    <p class="mt-2">Full Name: {{ message.sender }}</p>
                    <p>Email Address: {{ message.address }}</p>
                </section>
                <section>
                    <p class="mt-2">Subject: {{ message.subject }}</p>
                    <p>BCC: {{ message.bcc }}</p>
                </section>
                <section>
                    <p>CC: {{ message.cc }}</p>
                </section>
            </div>
            <div v-html="message.html" class="text-slate-400 py-4 my-5 px-3 text-center w-[40rem] mx-auto"></div>
        </Dialog>
    </div>
</template>


<script>
import Navbar from './Navbar.vue';
import IconCopy from './icons/IconCopy.vue';
import { useToast } from 'vue-toast-notification';
import ProgressBar from 'primevue/progressbar';
import IocnRefresh from './icons/IocnRefresh.vue';
import IconDelete from './icons/IconDelete.vue';
import IconGenerate from './icons/IconGenerate.vue';
import inBox from './inBox.vue';
import RecievedMsg from './RecievedMsg.vue';
import Dialog from 'primevue/dialog';




export default {
    components: {
        Navbar,
        IconCopy,
        ProgressBar,
        IocnRefresh,
        IconDelete,
        IconGenerate,
        inBox,
        RecievedMsg,
        Dialog
    },
    data() {
        return {
            tempMail: "",
            token: "",
            passWord: "youssef2000!@ER",
            isLoading: false,
            visible: false,
            isMsg: false,
            isWait: true,
            messages: [],
            message: {},
            intervalId: null
        }
    },
    beforeDestroy() {
        if (this.intervalId) {
            clearInterval(this.intervalId);
        }
    },
    methods: {
        toastNotification(message, type) {
            const $toast = useToast();
            $toast.open({
                message: message,
                type: type,
                duration: 1800,
                dismissible: true,
                position: 'top-right'
            });
        },

    },
}
</script>