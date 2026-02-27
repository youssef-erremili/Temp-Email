<template>
    <div class="min-h-screen bg-slate-900 flex flex-col">
        <div v-if="isLoading" class="h-1 w-full fixed top-0 z-50">
            <ProgressBar mode="indeterminate" style="height:3px;"></ProgressBar>
        </div>
        <Navbar />

        <main class="flex-grow flex flex-col items-center pt-20 pb-16 px-4 md:px-8">
            <div class="w-full max-w-5xl text-center space-y-12">
                <div class="space-y-6">
                    <h1 class="text-white capitalize font-bold text-4xl xl:text-5xl">
                        free temporary email generator
                        <span class="text-indigo-600">TempMail</span>
                    </h1>
                    <p class="max-w-3xl mx-auto text-slate-400 text-sm leading-relaxed">
                        Forget about spam, advertising mailings, hacking and attacking robots. Keep your real mailbox
                        clean and secure. Temp Mail provides temporary, secure, anonymous, free, disposable email
                        address.
                    </p>
                </div>

                <div class="py-12 w-full max-w-3xl mx-auto rounded-xl border-2 border-slate-600 border-dashed">
                    <h2 class="text-lg font-medium text-slate-100 mb-6">Your Temporary Email Address :</h2>

                    <div class="flex flex-col sm:flex-row justify-center items-center gap-4 px-6">
                        <div class="relative w-full max-w-lg">
                            <input v-model="tempMail" type="text" placeholder="tempMail address"
                                class="w-full bg-slate-800 rounded-full py-4 px-8 text-lg text-white font-sans border-none outline-none select-all"
                                name="tempmail" readonly>
                        </div>
                        <button @click="copyTempMail(tempMail)" type="button"
                            class="cursor-pointer rounded-full h-auto bg-indigo-600 p-4 hover:bg-indigo-700 transition-colors">
                            <IconCopy color="#fff" />
                        </button>
                    </div>
                </div>

                <div class="flex justify-center gap-4 max-w-4xl mx-auto">
                    <button :disabled="isLoading || cooldown > 0"
                        class="flex items-center gap-2 capitalize cursor-pointer text-sm font-bold rounded-full h-12 px-8 bg-yellow-400 text-slate-900 hover:bg-yellow-500 transition-all disabled:opacity-50 active:scale-95 shadow-lg shadow-yellow-400/10"
                        @click="generateFakeDomains()">
                        <IconGenerate class="w-5 h-5" color="currentColor" />
                        <span>{{ cooldown > 0 ? `wait ${cooldown}s` : 'generate' }}</span>
                    </button>
                    <button :disabled="isLoading || !tempMail"
                        class="flex items-center gap-2 capitalize cursor-pointer text-sm font-bold rounded-full h-12 px-8 bg-indigo-600 text-white hover:bg-indigo-700 transition-all disabled:opacity-50 active:scale-95 shadow-lg shadow-indigo-600/10"
                        @click="copyTempMail(tempMail)">
                        <IconCopy class="w-5 h-5" color="currentColor" />
                        <span>copy</span>
                    </button>
                </div>

                <inBox :isWait="isWait" class="w-full">
                    <RecievedMsg v-if="isMsg">
                        <div v-for="(msg, index) in messages" :key="index"
                            class="flex flex-col sm:flex-row items-center justify-between bg-white py-4 px-8 mb-3 rounded-2xl border border-slate-100 hover:border-indigo-200 transition-all hover:shadow-md group">
                            <div class="w-full sm:w-1/3 text-left">
                                <h3 class="font-bold text-slate-900 text-sm uppercase tracking-tight">{{ msg.fullname ||
                                    'Unknown' }}</h3>
                                <p class="text-xs text-indigo-600 font-medium">{{ msg.address }}</p>
                            </div>
                            <div class="w-full sm:w-1/3 text-center py-2 sm:py-0">
                                <p class="text-slate-600 text-sm font-medium">{{ msg.subject || '(No Subject)' }}</p>
                            </div>
                            <div class="w-full sm:w-1/3 text-right">
                                <button type="button" @click="getMessage(msg.id)"
                                    class="cursor-pointer bg-slate-900 rounded-full text-xs font-bold uppercase text-white py-2 px-6 hover:bg-indigo-600 transition-all">
                                    view
                                </button>
                            </div>
                        </div>
                    </RecievedMsg>
                </inBox>
            </div>
        </main>

        <Dialog v-model:visible="visible" maximizable modal header="Email Content" :style="{ width: '65rem' }"
            :breakpoints="{ '1199px': '75vw', '575px': '90vw' }" :pt="{
                root: { class: 'rounded-3xl overflow-hidden border-none shadow-2xl' },
                header: { class: 'bg-slate-900 text-white p-6' },
                content: { class: 'p-0' }
            }">
            <div
                class="m-0 bg-slate-50 text-slate-700 flex flex-col sm:flex-row justify-between gap-4 p-8 border-b border-slate-200">
                <section class="space-y-1">
                    <p class="text-xs uppercase font-bold text-slate-400">From</p>
                    <p class="text-slate-900 font-bold">{{ message.sender }}</p>
                    <p class="text-indigo-600 text-sm font-medium">{{ message.address }}</p>
                </section>
                <section class="space-y-1 sm:text-right">
                    <p class="text-xs uppercase font-bold text-slate-400">Subject</p>
                    <p class="text-slate-900 font-bold">{{ message.subject }}</p>
                    <p v-if="message.cc" class="text-slate-500 text-sm"><span class="font-bold">CC:</span> {{ message.cc
                        }}</p>
                </section>
            </div>
            <div class="p-8 bg-white">
                <div v-html="message.html" class="bg-white min-h-[400px] prose prose-indigo max-w-none"></div>
            </div>
        </Dialog>
    </div>
</template>



<script>
import Navbar from './Navbar.vue';
import IconCopy from './icons/IconCopy.vue';
import { useToast } from 'vue-toast-notification';
import ProgressBar from 'primevue/progressbar';
import IconGenerate from './icons/IconGenerate.vue';
import inBox from './inBox.vue';
import RecievedMsg from './RecievedMsg.vue';
import Dialog from 'primevue/dialog';

export default {
    components: {
        Navbar,
        IconCopy,
        ProgressBar,
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
            intervalId: null,
            cooldown: 0,
            cooldownInterval: null
        }
    },
    beforeUnmount() {
        if (this.intervalId) {
            clearInterval(this.intervalId);
        }
        if (this.cooldownInterval) {
            clearInterval(this.cooldownInterval);
        }
    },
    methods: {
        toastNotification(message, type) {
            const $toast = useToast();
            $toast.open({
                message: message,
                type: type,
                duration: 3000,
                dismissible: true,
                position: 'top-right'
            });
        },

        async generateFakeDomains() {
            if (this.isLoading || this.cooldown > 0) return;

            this.tempMail = "Generating..."
            this.isLoading = true
            this.messages = [];
            this.message = {};
            this.isWait = true;
            this.isMsg = false;

            if (this.intervalId) {
                clearInterval(this.intervalId);
                this.intervalId = null;
            }

            try {
                // 1. Get random identity for the email prefix
                const userResponse = await fetch("https://randomuser.me/api/");
                if (!userResponse.ok) throw new Error('Identity service unavailable');
                const userData = await userResponse.json();
                const firstName = userData.results[0].name.first.toLowerCase().replace(/[^a-z0-9]/g, '');
                const lastName = userData.results[0].name.last.toLowerCase().replace(/[^a-z0-9]/g, '');
                const randomNum = Math.floor(100 + Math.random() * 899);
                const userName = `${firstName}${lastName}${randomNum}`;

                // 2. Get available domains
                const domainsResponse = await fetch('https://api.mail.tm/domains');
                if (!domainsResponse.ok) throw new Error('Mail domain service unavailable');
                const domainsData = await domainsResponse.json();

                if (!domainsData['hydra:member'] || domainsData['hydra:member'].length === 0) {
                    throw new Error('No temporary domains available');
                }
                const domain = domainsData['hydra:member'][0].domain;
                this.tempMail = `${userName}@${domain}`;

                // 3. Create account
                const accountResponse = await fetch('https://api.mail.tm/accounts', {
                    method: 'POST',
                    headers: { 'Content-Type': 'application/json' },
                    body: JSON.stringify({
                        address: this.tempMail,
                        password: this.passWord
                    })
                });

                if (!accountResponse.ok) {
                    const errorDetails = await accountResponse.json().catch(() => ({}));
                    if (errorDetails.message === "Address already exists") {
                        return this.generateFakeDomains();
                    }
                    throw new Error('Could not create temporary account');
                }

                // 4. Get authentication token
                const tokenResponse = await fetch('https://api.mail.tm/token', {
                    method: 'POST',
                    headers: { 'Content-Type': 'application/json' },
                    body: JSON.stringify({
                        address: this.tempMail,
                        password: this.passWord
                    })
                });

                if (!tokenResponse.ok) throw new Error('Authentication failed');
                const tokenData = await tokenResponse.json();
                this.token = tokenData.token;

                this.toastNotification('Temporary email ready!', 'success');

                this.fetchMessages();
                this.intervalId = setInterval(() => {
                    this.fetchMessages();
                }, 10000);

            } catch (error) {
                console.error('Email Generation Error:', error);
                this.tempMail = "Error";
                this.toastNotification(error.message || 'Verification service failed', 'error');
            } finally {
                this.isLoading = false;
                this.startCooldown();
            }
        },

        startCooldown() {
            this.cooldown = 3;
            if (this.cooldownInterval) clearInterval(this.cooldownInterval);
            this.cooldownInterval = setInterval(() => {
                if (this.cooldown > 0) {
                    this.cooldown--;
                } else {
                    clearInterval(this.cooldownInterval);
                }
            }, 1000);
        },

        async fetchMessages() {
            if (!this.token) return;

            try {
                const response = await fetch('https://api.mail.tm/messages', {
                    method: 'GET',
                    headers: { 'Authorization': `Bearer ${this.token}` }
                });

                if (!response.ok) {
                    if (response.status === 401) {
                        clearInterval(this.intervalId);
                        this.intervalId = null;
                        this.toastNotification('Session expired, please refresh', 'warning');
                        return;
                    }
                    throw new Error('Failed to check inbox');
                }

                const data = await response.json();
                const members = data['hydra:member'] || [];

                if (members.length === 0) {
                    this.isMsg = false;
                    this.isWait = true;
                } else {
                    this.isMsg = true;
                    this.isWait = false;

                    members.forEach(msg => {
                        const newMessage = {
                            fullname: msg.from.name || 'Anonymous',
                            address: msg.from.address,
                            subject: msg.subject || '(No Subject)',
                            id: msg.id,
                        };

                        if (!this.messages.find(m => m.id === newMessage.id)) {
                            this.messages.unshift(newMessage);
                        }
                    });
                }
            } catch (error) {
                console.error('Fetch Messages Error:', error);
            }
        },

        async getMessage(messageId) {
            try {
                const response = await fetch(`https://api.mail.tm/messages/${messageId}`, {
                    method: 'GET',
                    headers: {
                        'Authorization': `Bearer ${this.token}`,
                        'Content-Type': 'application/json'
                    }
                });

                if (!response.ok) throw new Error('Could not retrieve email content');

                const data = await response.json();
                this.message = {
                    sender: data.from.name || 'Anonymous',
                    address: data.from.address,
                    subject: data.subject || '(No Subject)',
                    bcc: data.bcc && data.bcc.length > 0 ? data.bcc[0].address : "None",
                    cc: data.cc && data.cc.length > 0 ? data.cc[0].address : "None",
                    html: data.html || `<div class="p-4 text-slate-500 italic">${data.text || '(Empty messages body)'}</div>`
                };
                this.visible = true;
            } catch (error) {
                this.toastNotification('Failed to open message', 'error');
            }
        },

        refreshPage() {
            this.generateFakeDomains();
        },

        copyTempMail(text) {
            if (!text || text === "Error") return;

            navigator.clipboard.writeText(text)
                .then(() => {
                    this.toastNotification('Address copied to clipboard', 'info')
                })
                .catch(() => {
                    this.toastNotification('Copy failed', 'error');
                });
        },
    },

    mounted() {
        this.generateFakeDomains();
    }
}
</script>
